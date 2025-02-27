<template>
  <div id="app" class="container mt-5">
    <h1 class="border-bottom pb-2 mb-4">Vue pivot table demo</h1>

    <h2 class="border-bottom pb-2 mb-4">Pivot <small>(drag & drop UI + PivotTable)</small></h2>

    <div class="mb-5">
      <pivot
        :data="data"
        :fields="fields"
        :available-field-keys="availableFieldKeys"
        :row-field-keys="rowFieldKeys"
        :col-field-keys="colFieldKeys"
        :reducer="reducer"
        :default-show-settings="defaultShowSettings">
        <template v-slot:value="{ value }">
          {{ value | number }}
        </template>
        <template v-slot:countryFlagHeader="{ value }">
          {{ countryEmoji(value) }}
        </template>
        <template v-slot:countryNameHeader="{ value }">
          {{ value | capitalize }}
        </template>
        <template v-slot:genderHeader="{ value }">
          <svg v-if="value == 'female'" aria-hidden="true" data-prefix="fas" data-icon="venus" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 288 512" class="svg-inline--fa fa-venus fa-fw"><path fill="currentColor" d="M288 176c0-79.5-64.5-144-144-144S0 96.5 0 176c0 68.5 47.9 125.9 112 140.4V368H76c-6.6 0-12 5.4-12 12v40c0 6.6 5.4 12 12 12h36v36c0 6.6 5.4 12 12 12h40c6.6 0 12-5.4 12-12v-36h36c6.6 0 12-5.4 12-12v-40c0-6.6-5.4-12-12-12h-36v-51.6c64.1-14.5 112-71.9 112-140.4zm-224 0c0-44.1 35.9-80 80-80s80 35.9 80 80-35.9 80-80 80-80-35.9-80-80z" class=""></path></svg>
          <svg v-else-if="value == 'male'" aria-hidden="true" data-prefix="fas" data-icon="mars" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 384 512" class="svg-inline--fa fa-mars fa-fw"><path fill="currentColor" d="M372 64h-79c-10.7 0-16 12.9-8.5 20.5l16.9 16.9-80.7 80.7c-22.2-14-48.5-22.1-76.7-22.1C64.5 160 0 224.5 0 304s64.5 144 144 144 144-64.5 144-144c0-28.2-8.1-54.5-22.1-76.7l80.7-80.7 16.9 16.9c7.6 7.6 20.5 2.2 20.5-8.5V76c0-6.6-5.4-12-12-12zM144 384c-44.1 0-80-35.9-80-80s35.9-80 80-80 80 35.9 80 80-35.9 80-80 80z" class=""></path></svg>
          {{ value | capitalize }}
        </template>
        <template v-slot:computing>
          <div class="position-absolute w-100 h-100 text-center" style="z-index: 1;">
            <div class="position-sticky bg-white d-inline-block mt-5 p-3" style="top: 0;">
              <svg aria-hidden="true" data-prefix="fas" data-icon="spinner" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="svg-inline--fa fa-spinner fa-fw fa-pulse"><path fill="currentColor" d="M304 48c0 26.51-21.49 48-48 48s-48-21.49-48-48 21.49-48 48-48 48 21.49 48 48zm-48 368c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.49-48-48-48zm208-208c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.49-48-48-48zM96 256c0-26.51-21.49-48-48-48S0 229.49 0 256s21.49 48 48 48 48-21.49 48-48zm12.922 99.078c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48c0-26.509-21.491-48-48-48zm294.156 0c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48c0-26.509-21.49-48-48-48zM108.922 60.922c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.491-48-48-48z" class=""></path></svg>
              Loading table values...
            </div>
          </div>
        </template>
      </pivot>
    </div>

    <h2 class="border-bottom pb-2 mb-4">PivotTable <small>(standalone)</small></h2>

    <div class="mb-5">
      <pivot-table
        :data="asyncData"
        :row-fields="rowFields"
        :col-fields="colFields"
        :reducer="reducer"
        :default-show-settings="defaultShowSettings"
        :is-data-loading="isDataLoading">
        <template v-slot:value="{ value }">
          {{ value | number }}
        </template>
        <template v-slot:countryFlagHeader="{ value }">
          {{ countryEmoji(value) }}
        </template>
        <template v-slot:countryNameHeader="{ value }">
          {{ value | capitalize }}
        </template>
        <template v-slot:genderHeader="{ value }">
          <svg v-if="value == 'female'" aria-hidden="true" data-prefix="fas" data-icon="venus" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 288 512" class="svg-inline--fa fa-venus fa-fw"><path fill="currentColor" d="M288 176c0-79.5-64.5-144-144-144S0 96.5 0 176c0 68.5 47.9 125.9 112 140.4V368H76c-6.6 0-12 5.4-12 12v40c0 6.6 5.4 12 12 12h36v36c0 6.6 5.4 12 12 12h40c6.6 0 12-5.4 12-12v-36h36c6.6 0 12-5.4 12-12v-40c0-6.6-5.4-12-12-12h-36v-51.6c64.1-14.5 112-71.9 112-140.4zm-224 0c0-44.1 35.9-80 80-80s80 35.9 80 80-35.9 80-80 80-80-35.9-80-80z" class=""></path></svg>
          <svg v-else-if="value == 'male'" aria-hidden="true" data-prefix="fas" data-icon="mars" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 384 512" class="svg-inline--fa fa-mars fa-fw"><path fill="currentColor" d="M372 64h-79c-10.7 0-16 12.9-8.5 20.5l16.9 16.9-80.7 80.7c-22.2-14-48.5-22.1-76.7-22.1C64.5 160 0 224.5 0 304s64.5 144 144 144 144-64.5 144-144c0-28.2-8.1-54.5-22.1-76.7l80.7-80.7 16.9 16.9c7.6 7.6 20.5 2.2 20.5-8.5V76c0-6.6-5.4-12-12-12zM144 384c-44.1 0-80-35.9-80-80s35.9-80 80-80 80 35.9 80 80-35.9 80-80 80z" class=""></path></svg>
          {{ value | capitalize }}
        </template>
        <template v-slot:loading>
          <div class="text-center">
            <svg aria-hidden="true" data-prefix="fas" data-icon="spinner" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="svg-inline--fa fa-spinner fa-fw fa-pulse"><path fill="currentColor" d="M304 48c0 26.51-21.49 48-48 48s-48-21.49-48-48 21.49-48 48-48 48 21.49 48 48zm-48 368c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.49-48-48-48zm208-208c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.49-48-48-48zM96 256c0-26.51-21.49-48-48-48S0 229.49 0 256s21.49 48 48 48 48-21.49 48-48zm12.922 99.078c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48c0-26.509-21.491-48-48-48zm294.156 0c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48c0-26.509-21.49-48-48-48zM108.922 60.922c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.491-48-48-48z" class=""></path></svg>
            Loading...
          </div>
        </template>
        <template v-slot:computing>
          <div class="position-absolute w-100 h-100 text-center" style="z-index: 1;">
            <div class="position-sticky bg-white d-inline-block mt-5 p-3" style="top: 0;">
              <svg aria-hidden="true" data-prefix="fas" data-icon="spinner" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="svg-inline--fa fa-spinner fa-fw fa-pulse"><path fill="currentColor" d="M304 48c0 26.51-21.49 48-48 48s-48-21.49-48-48 21.49-48 48-48 48 21.49 48 48zm-48 368c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.49-48-48-48zm208-208c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.49-48-48-48zM96 256c0-26.51-21.49-48-48-48S0 229.49 0 256s21.49 48 48 48 48-21.49 48-48zm12.922 99.078c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48c0-26.509-21.491-48-48-48zm294.156 0c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48c0-26.509-21.49-48-48-48zM108.922 60.922c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.491-48-48-48z" class=""></path></svg>
              Loading table values...
            </div>
          </div>
        </template>
      </pivot-table>
    </div>

    <h2 class="border-bottom pb-2 mb-4">Pivot with mixed-type data</h2>

    <div class="mb-5">
      <pivot
        :data="dataGdpFlattened"
        :fields="fieldsGdp"
        :available-field-keys="availableFieldKeysGdp"
        :row-field-keys="rowFieldKeysGdp"
        :col-field-keys="colFieldKeysGdp"
        :reducer="reducerGdp"
        :reducer-initial-value="{ count: 0, gdp: 0 }"
        :default-show-settings="defaultShowSettings">
        <template v-slot:value="{ value, labels }">
          <mixed-data-value :value="value" :labels="labels" />
        </template>
        <template v-slot:countryFlagHeader="{ value }">
          {{ countryEmoji(value) }}
        </template>
        <template v-slot:countryNameHeader="{ value }">
          {{ value | capitalize }}
        </template>
        <template v-slot:typeHeader="{ value }">
          <template v-if="value === 'count'">Population</template>
          <template v-if="value === 'gdp'">GDP</template>
        </template>
        <template v-slot:computing>
          <div class="position-absolute w-100 h-100 text-center" style="z-index: 1;">
            <div class="position-sticky bg-white d-inline-block mt-5 p-3" style="top: 0;">
              <svg aria-hidden="true" data-prefix="fas" data-icon="spinner" role="img" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="svg-inline--fa fa-spinner fa-fw fa-pulse"><path fill="currentColor" d="M304 48c0 26.51-21.49 48-48 48s-48-21.49-48-48 21.49-48 48-48 48 21.49 48 48zm-48 368c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.49-48-48-48zm208-208c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.49-48-48-48zM96 256c0-26.51-21.49-48-48-48S0 229.49 0 256s21.49 48 48 48 48-21.49 48-48zm12.922 99.078c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48c0-26.509-21.491-48-48-48zm294.156 0c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48c0-26.509-21.49-48-48-48zM108.922 60.922c-26.51 0-48 21.49-48 48s21.49 48 48 48 48-21.49 48-48-21.491-48-48-48z" class=""></path></svg>
              Loading table values...
            </div>
          </div>
        </template>
      </pivot>
    </div>
  </div>
</template>

<script>
import Pivot from './components/Pivot'
import PivotTable from './components/PivotTable'
import MixedDataValue from './MixedDataValue'
import data from './data'
import dataGdp from './data-gdp'

const dataGdpFlattened = []
dataGdp.forEach(item => {
  dataGdpFlattened.push({ country: item.country, year: item.year, type: 'count', count: item.count })
  dataGdpFlattened.push({ country: item.country, year: item.year, type: 'gdp', gdp: item.gdp })
})

export default {
  name: 'app',
  components: { Pivot, PivotTable, MixedDataValue },
  data: () => {
    return {
      data,
      asyncData: [],
      dataGdpFlattened,

      // Pivot params
      fields: [{
        key: 'country',
        getter: item => item.country,
        label: 'Country',
        headers: [{
          slotName: 'countryFlagHeader',
          label: 'Flag',
          checked: true
        }, {
          slotName: 'countryNameHeader',
          label: 'Name',
          checked: true
        }],
        headerAttributeFilter: true,
        valueFilter: true
      }, {
        key: 'gender',
        getter: item => item.gender,
        label: 'Gender',
        headerSlotName: 'genderHeader',
        valueFilter: true,
        valueFilterSlotName: 'genderHeader'
      }, {
        key: 'year',
        getter: item => item.year,
        label: 'Year',
        valueFilter: true
      }],
      availableFieldKeys: [],
      rowFieldKeys: ['country', 'gender'],
      colFieldKeys: ['year'],
      reducer: (acc, item) => acc + item.count,
      defaultShowSettings: true,
      isDataLoading: false,

      // Pivot table standalone field params
      rowFields: [{
        getter: item => item.country,
        label: 'Country',
        headerSlotNames: ['countryFlagHeader', 'countryNameHeader']
      }, {
        getter: item => item.gender,
        label: 'Gender',
        headerSlotName: 'genderHeader'
      }],
      colFields: [{
        getter: item => item.year,
        label: 'Year'
      }],

      // Pivot with advanced reducer
      fieldsGdp: [{
        key: 'country',
        getter: item => item.country,
        label: 'Country',
        headers: [{
          slotName: 'countryFlagHeader',
          label: 'Flag',
          checked: true
        }, {
          slotName: 'countryNameHeader',
          label: 'Name',
          checked: true
        }],
        headerAttributeFilter: true,
        valueFilter: true
      }, {
        key: 'year',
        getter: item => item.year,
        label: 'Year',
        valueFilter: true
      }, {
        key: 'type',
        getter: item => item.type,
        headerSlotName: 'typeHeader',
        label: 'Type',
        valueFilter: true,
        valueFilterSlotName: 'typeHeader'
      }],
      availableFieldKeysGdp: [],
      rowFieldKeysGdp: ['country', 'type'],
      colFieldKeysGdp: ['year'],
      reducerGdp: (acc, item) => {
        if (item.type === 'count') acc.count += item.count
        else if (item.type === 'gdp') acc.gdp += item.gdp
        return acc
      }
    }
  },
  methods: {
    countryCode: function(country) {
      switch (country) {
        case 'Australia':
          return 'au'
        case 'China':
          return 'cn'
        case 'France':
          return 'fr'
        case 'India':
          return 'in'
        case 'United States':
          return 'us'
      }
    },
    countryEmoji: function(country) {
      const countryCode = this.countryCode(country)
      const codePoints = countryCode
        .toUpperCase()
        .split('')
        .map(char =>  127397 + char.charCodeAt())
      return String.fromCodePoint(...codePoints)
    }
  },
  created: function() {
    // Simulate async data loading
    this.isDataLoading = true
    setTimeout(() => {
      this.asyncData = data
      this.isDataLoading = false
    }, 3000)
  },
  filters: {
    number: function(value) {
      return value.toLocaleString()
    },
    capitalize: function(value) {
      return value.replace(/\b\w/g, l => l.toUpperCase())
    },
    amount: function(value) {
      return value.toLocaleString('en-US', {
        style: 'currency',
        currency: 'USD',
        maximumFractionDigits: 2
      })
    }
  }
}
</script>

<style lang="scss">
$enable-rounded: false;
$table-cell-padding: .5rem; // default in bs5
$table-cell-padding-sm: .25rem; // default in bs5
@import '~bootstrap/scss/bootstrap.scss';

/* Table with aria-busy attribute */
table[aria-busy='true'] {
  opacity: 0.6;
}

/* FontAwesome icons */
.svg-inline--fa.fa-fw {
  width: 1.25em;
}

.svg-inline--fa {
  display: inline-block;
  font-size: inherit;
  height: 1em;
  overflow: visible;
  vertical-align: -.125em !important;
}

.fa-pulse {
  animation: fa-spin 1s infinite steps(8);
}

@keyframes fa-spin {
  0% {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(1turn);
  }
}
</style>
