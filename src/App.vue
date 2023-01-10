<template>
  <div id="app">
    <h1 style="margin-bottom: 30px;">ПОИСК БРЕНДОВ ПО WILDBERIES</h1>

    <span class="">Введи строку поиска</span>

    <div>
      <input class="input" type="text" v-model="searchString" @input="fieldType" @keyup.enter="doSearch()" >
    </div>

    <span class="">Введи Имя бренда</span>

    <div>
      <input class="input" type="text" v-model="brandName" >
    </div>

    <div>
      <button class="btn" @click="doSearch()" style="margin-top: 10px;">ПОИСК</button>
    </div>  
    
    <div class="divider" />

    <div v-if="calculateItemsLength()" class="info">
      <span>Список полученных брендов</span>
      <div class="info_row">
        <span>Всего брендов: {{ brandItems.length }}</span>
        <span>Вхождение искомого бренда: {{ brandFound?'Есть вхождение':'Нет вхождения' }}</span>
      </div>  
    </div>

    <span class="ess" v-if="emptySearchString" >ВНИМАНИЕ !!! НЕ ЗАДАНА СТРОКА ПОИСКА</span>
    <span class="emptyArray" v-if="emptySearchResult" >УВЫ. ПОИСК НИЧЕГО НЕ ДАЛ. ПЕРЕФРАЗИРУЙТЕ ЗАПРОС.</span>

    <div class="table">
      <div v-for="( item, key ) in brandItems" :key="key" class="cell" :class="{ brandMatching: brandMatching( item.name ) }">
        <span class="cell_key">{{ key }}</span>
        <span class="cell_value">{{ item.name }}</span>
      </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios'
export default {
  name: 'App',

  data() {
    return {
      searchString: '',
      brandName: '',
      brandItems: [],
      emptySearchString: false,
      emptySearchResult: false,
      brandFound: false,
    }
  },

  methods: {

    calculateItemsLength() {
      if ( this.brandItems.length > 0 )
        return true
      else 
        return false  
    },

    brandMatching( brandItem ) {
      if ( this.brandName && this.brandName == brandItem ) {
        this.brandFound = true
        return true
      } else 
        return false  
    },

    fieldType( e ) {
      if( e.target.value )
        this.emptySearchString = false
    },

    async doSearch() {
      if ( !this.searchString ) {
        this.emptySearchString = true
        return
      }
      this.emptySearchString = false
      this.emptySearchResult = false
      await axios.get( 'https://search.wb.ru/exactmatch/ru/common/v4/search', {
        params: {
          filters: 'fbrand',
          resultset: 'filters',
          dest: '1,1,1,1',
          query: this.searchString,
        }
      } )
        .then ( result => {
          result = result.data
          if ( result.data && result.data.filters && typeof result.data.filters == 'object' ) {
            if ( result.data.filters.length > 0 ) {
              this.brandItems = result.data.filters[ 0 ].items
            }
          } else {
            this.brandItems = []
            this.emptySearchResult = true
          }
        } )
    }
  }

}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 30px;

  display: flex;
  flex-direction: column;
  gap: 10px;
  width: 100%;
  justify-content: center;
}

.input {
  padding: 5px;
  font-size: 1.3em;
  max-width: 500px;
  width: 100%;
}

.btn {
  padding: 5px 30px;
  border: none;
  border-radius: 5px;
  background-color: lightgray;
  color: black;
  font-weight: bold;
  font-size: 1.2em;
}

.divider {
  border: none;
  border-bottom: 1px dotted silver;
  margin: 10px 20%;
}

.ess {
  font-weight: bold;
  font-size: 1.5em;
  color: red;
}

.emptyArray {
  font-weight: bold;
  font-size: 1.5em;
}

.table {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(210px, 1fr));
  grid-gap: 10px;
  grid-auto-flow: dense;
}

.cell {
  min-width: max-content;
  padding: 10px;
  background-color: #efefef;
  border-radius: 10px;
  border: 5px solid white;
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  align-items: center;
}

.cell_key {
  margin-right: 10px;
  font-size: 0.7em;
}

.cell_value {
  font-weight: bold;
}

.brandMatching {
  background-color: #1e2563 !important;
  color: white !important;
}

.info {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  margin: 20px 0px;
}

.info_row { 
  display: flex;
  flex-direction: row;
  gap: 20px;
  font-size: 1.2em;
}
</style>
