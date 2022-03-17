<template>
    <div>
        <!-- Top row -->
        <div v-if="showSettings" class="d-flex flex-nowrap gutter mb-3">
            <div class="left-col">
                <button class="btn btn-primary" @click="toggleShowSettings">
                    {{ hideSettingsText }}
                </button>
            </div>
        </div>

        <div v-else class="mb-3">
            <button class="btn btn-primary" @click="toggleShowSettings">
                {{ showSettingsText }}
            </button>
        </div>

        <div class="grid-y" v-if="showSettings">
            <div class="cell">
                <div class="grid-x">
                    <!-- Values -->
                    <div class="cell shrink">
                        <div class="drag-area border-primary" :class="dragAreaClass">
                            <div class="drag-area-title mb-3">{{ valuesLabelText }}</div>
                            <draggable
                                class="drag-area-zone gutter-sm"
                                group="displayedElements"
                                handle=".btn-draggable"
                                @start="start"
                                @end="end"
                            >
                                <div v-for="key in valuesKeys" :key="key" class="field">
                                    <field-label
                                        :field="displayedElements[key]"
                                        :fieldValues="displayedElementsValues"
                                        :select-all-text="selectAllText"
                                        :unselect-all-text="unselectAllText"
                                    >
                                        <!-- pass down scoped slots -->
                                        <template v-for="(_, slot) of $scopedSlots" v-slot:[slot]="scope"
                                            ><slot :name="slot" v-bind="scope"
                                        /></template>
                                    </field-label>
                                </div>
                            </draggable>
                        </div>
                    </div>
                    <!-- Available fields for values -->
                    <div class="cell auto">
                        <div class="drag-area border-primary" :class="dragAreaClass">
                            <div class="drag-area-title mb-3">{{ availableValuesLabelText }}</div>
                            <draggable
                                class="drag-area-zone gutter-sm"
                                group="displayedElements"
                                handle=".btn-draggable"
                                @start="start"
                                @end="end"
                            >
                            </draggable>
                        </div>
                    </div>
                </div>
            </div>
            <!-- Column fields -->
            <div class="cell auto">
                <div class="drag-area border-primary" :class="dragAreaClass">
                    <div class="drag-area-title mb-3">{{ colsLabelText }}</div>
                    <draggable
                        v-model="internal.colFieldKeys"
                        class="d-flex flex-row gutter-sm drag-area-zone"
                        group="fields"
                        handle=".btn-draggable"
                        @start="start"
                        @end="end"
                    >
                        <div v-for="key in internal.colFieldKeys" :key="key" class="field">
                            <field-label
                                :field="fieldsWithValues[key]"
                                v-model="fieldValues[key]"
                                :select-all-text="selectAllText"
                                :unselect-all-text="unselectAllText"
                            >
                                <!-- pass down scoped slots -->
                                <template v-for="(_, slot) of $scopedSlots" v-slot:[slot]="scope"
                                    ><slot :name="slot" v-bind="scope"
                                /></template>
                            </field-label>
                        </div>
                    </draggable>
                </div>
            </div>

            <div class="cell">
                <div class="grid-x">
                    <!-- Row fields -->
                    <div v-if="showSettings" class="cell shrink">
                        <div class="drag-area border-primary" :class="dragAreaClass">
                            <div class="drag-area-title mb-3">{{ rowsLabelText }}</div>
                            <draggable
                                v-model="internal.rowFieldKeys"
                                class="grid-x drag-area-zone"
                                group="fields"
                                handle=".btn-draggable"
                                @start="start"
                                @end="end"
                            >
                                <div v-for="key in internal.rowFieldKeys" :key="key" class="cell field">
                                    <field-label
                                        :field="fieldsWithValues[key]"
                                        v-model="fieldValues[key]"
                                        :select-all-text="selectAllText"
                                        :unselect-all-text="unselectAllText"
                                    >
                                        <!-- pass down scoped slots -->
                                        <template v-for="(_, slot) of $scopedSlots" v-slot:[slot]="scope"
                                            ><slot :name="slot" v-bind="scope"
                                        /></template>
                                    </field-label>
                                </div>
                            </draggable>
                        </div>
                    </div>

                    <!-- Available fields -->
                    <div class="cell shrink drag-area" :class="dragAreaClass">
                        <div class="drag-area-title 3mb-">{{ availableFieldsLabelText }}</div>
                        <draggable
                            v-model="internal.availableFieldKeys"
                            class="drag-area-zone"
                            group="fields"
                            handle=".btn-draggable"
                            @start="start"
                            @end="end"
                        >
                            <div v-for="key in internal.availableFieldKeys" :key="key" class="field">
                                <field-label
                                    :field="fieldsWithValues[key]"
                                    v-model="fieldValues[key]"
                                    :select-all-text="selectAllText"
                                    :unselect-all-text="unselectAllText"
                                    variant="secondary"
                                >
                                    <!-- pass down scoped slots -->
                                    <template v-for="(_, slot) of $scopedSlots" v-slot:[slot]="scope"
                                        ><slot :name="slot" v-bind="scope"
                                    /></template>
                                </field-label>
                            </div>
                        </draggable>
                    </div>
                </div>
            </div>
        </div>

        <!-- Table -->
        <div class="flex-fill" :style="tableWrapperStyle">
            <pivot-table
                :data="data"
                :row-fields="rowFields"
                :col-fields="colFields"
                :reducer="reducer"
                :reducer-initial-value="reducerInitialValue"
                :no-data-warning-text="noDataWarningText"
                :is-data-loading="isDataLoading"
            >
                <!-- pass down scoped slots -->
                <template v-for="(_, slot) of $scopedSlots" v-slot:[slot]="scope"
                    ><slot :name="slot" v-bind="scope"
                /></template>
            </pivot-table>
        </div>
    </div>
</template>

<script>
import FieldLabel from './FieldLabel.vue';
import PivotTable from './PivotTable.vue';
import Draggable from 'vuedraggable';
import naturalSort from 'javascript-natural-sort';
import Accordion from '@/components/accordion';
import AccordionItem from '@/components/accordion_item';
import CheckboxField from '@/components/checkbox_field';

export default {
    name: 'vue-pivot',
    components: { FieldLabel, PivotTable, Draggable, Accordion, AccordionItem, CheckboxField },
    props: {
        data: {
            type: Array,
            default: () => [],
        },
        fieldsPivot: {
            type: Array,
            default: () => [],
        },
        valuesKeys: {
            type: Array,
            default: () => [],
        },
        availableFieldKeys: {
            type: Array,
            default: () => [],
        },
        rowFieldKeys: {
            type: Array,
            default: () => [],
        },
        colFieldKeys: {
            type: Array,
            default: () => [],
        },
        reducer: {
            type: Function,
            default: (sum) => sum + 1,
        },
        reducerInitialValue: {
            default: 0,
        },
        accordion_id: {
            default: 1,
        },
        defaultShowSettings: {
            type: Boolean,
            default: true,
        },
        availableFieldsLabelText: {
            type: String,
            default: 'Champs disponibles',
        },
        availableValuesLabelText: {
            type: String,
            default: 'Champs disponibles (pour les valeurs)',
        },
        colsLabelText: {
            type: String,
            default: 'Colonnes',
        },
        rowsLabelText: {
            type: String,
            default: 'Lignes',
        },
        valuesLabelText: {
            type: String,
            default: 'Valeurs à afficher',
        },
        hideSettingsText: {
            type: String,
            default: 'Cacher les paramètres',
        },
        showSettingsText: {
            type: String,
            default: 'Afficher les paramètres',
        },
        noDataWarningText: {
            type: String,
            default: 'Pas de données à afficher.',
        },
        selectAllText: {
            type: String,
            default: 'Tout sélectionner',
        },
        unselectAllText: {
            type: String,
            default: 'Tout déselectionner',
        },
        isDataLoading: {
            type: Boolean,
            default: false,
        },
    },
    data: function () {
        const fieldValues = {};
        this.fieldsPivot
            .filter((field) => field.valueFilter)
            .forEach((field) => {
                fieldValues[field.key] = {};
            });

        return {
            internal: {
                availableFieldKeys: this.availableFieldKeys,
                rowFieldKeys: this.rowFieldKeys,
                colFieldKeys: this.colFieldKeys,
            },
            displayedElements: {
                nbActions: { label: "nombre d'actions", b: 2 },
                timeActions: { label: 'temps des actions', d: 4 },
            },
            displayedElementsValues: [],
            fieldValues,
            dragging: false,
            showSettings: true,
        };
    },
    computed: {
        // Fields with values extracted from data (if field has valueFilter)
        fieldsWithValues: function () {
            // Create object: field.key => field
            const fieldsWithValues = {};

            this.fieldsPivot.forEach((field) => {
                fieldsWithValues[field.key] = field;
            });

            // Add valuesSet
            const valueFilterableFields = this.fieldsPivot.filter((field) => field.valueFilter);

            // Create valuesSet for each value filterable field
            valueFilterableFields.forEach((field) => {
                fieldsWithValues[field.key].valuesSet = new Set();
            });

            // Iterate on data once
            this.data.forEach((item) => {
                valueFilterableFields.forEach((field) => {
                    fieldsWithValues[field.key].valuesSet.add(field.getter(item));
                });
            });

            // Creates values sorted from valuesSet
            valueFilterableFields.forEach((field) => {
                fieldsWithValues[field.key].values = Array.from(fieldsWithValues[field.key].valuesSet).sort(
                    field.sort || naturalSort,
                );
            });
            console.log('fieldValues : ', this.fieldValues);
            console.log('field with values : ', fieldsWithValues);
            return fieldsWithValues;
        },
        // Fields selected values as set
        valuesFiltered: function () {
            const valuesFiltered = {};

            for (let [key, valuesObject] of Object.entries(this.fieldValues)) {
                valuesFiltered[key] = new Set();
                valuesObject.forEach((valueObject) => {
                    if (valueObject.checked) {
                        valuesFiltered[key].add(valueObject.value);
                    }
                });
            }

            return valuesFiltered;
        },
        // Pivot table props from Pivot props & data
        rowFields: function () {
            const rowFields = [];

            this.internal.rowFieldKeys.forEach((key) => {
                const field = this.fieldsPivot.find((field) => field.key === key);

                // Generate headerSlotNames from headers
                if (field.headers) {
                    field.headerSlotNames = field.headers
                        .filter((header) => header.checked)
                        .map((header) => header.slotName);
                    console.log(field.headerSlotNames);
                }
                console.log('check');
                // Add selected values
                if (field.valueFilter) {
                    field.valuesFiltered = this.valuesFiltered[field.key];
                }

                rowFields.push(field);
            });

            return rowFields;
        },
        colFields: function () {
            const colFields = [];

            this.internal.colFieldKeys.forEach((key) => {
                const field = this.fieldsPivot.find((field) => field.key === key);

                // Generate headerSlotNames from headers
                if (field.headers) {
                    field.headerSlotNames = field.headers
                        .filter((header) => header.checked)
                        .map((header) => header.slotName);
                    console.log(field.headerSlotNames);
                }
                console.log('check2');

                // Add selected values
                if (field.valueFilter) {
                    field.valuesFiltered = this.valuesFiltered[field.key];
                }
                colFields.push(field);
            });
            console.log(colFields);
            return colFields;
        },
        // Drag area class
        dragAreaClass: function () {
            return this.dragging ? 'drag-area-highlight' : null;
        },
        // Table wrapper style
        tableWrapperStyle: function () {
            const maxWidth = '100%'; // this.showSettings ? 'calc(100% - 200px - 2rem)' : '100%'
            return `max-width: ${maxWidth};`;
        },
    },
    methods: {
        // Toggle settings
        toggleShowSettings: function () {
            this.showSettings = !this.showSettings;
        },
        // Update dragging boolean
        start: function () {
            this.dragging = true;
        },
        end: function () {
            this.dragging = false;
        },
        // Update fieldValues
        updateFieldValues: function () {
            for (let [key, field] of Object.entries(this.fieldsWithValues)) {
                if (field.valueFilter) {
                    this.fieldValues[key] = [];
                    field.values.forEach((value) => {
                        this.fieldValues[key].push({ value, checked: true });
                    });
                }
            }
        },
    },
    watch: {
        data: function () {
            this.updateFieldValues();
        },
        values: function () {
            return this.values;
        },
    },
    created: function () {
        console.log('values', this.values);
        this.showSettings = this.defaultShowSettings;
        this.updateFieldValues();
    },
};
</script>

<style lang="scss" scoped>
/* Left column width */
.left-col {
    min-width: 200px;
    max-width: 200px;
}

/* Grid with gutter */
.gutter,
.gutter-y {
    margin-top: -1rem;

    > * {
        margin-top: 1rem;
    }
}
.gutter-x,
.gutter {
    margin-left: -1rem;
    > * {
        margin-left: 1rem;
    }
}

.gutter-sm,
.gutter-y-sm {
    margin-top: -0.5rem;

    > * {
        margin-top: 0.5rem;
    }
}
.gutter-x-sm,
.gutter-sm {
    margin-left: -0.5rem;
    > * {
        margin-left: 0.5rem;
    }
}

/* Drag & drop stuff */
.drag-area {
    border: 1px dashed #ccc;
    padding: 0.75rem;
    transition: background-color 0.4s;

    .drag-area-title {
        line-height: 1;
    }

    .drag-area-zone {
        min-width: 10rem;
        min-height: 46px;
    }
}

.drag-area-highlight {
    background-color: #f3f3f3;
}

.sortable-ghost {
    opacity: 0.4;
}

.field {
    position: relative;
}
.btn {
    margin: 1rem;
}
</style>
