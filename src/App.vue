<template>
  <div id="app">
    <input type="text" v-model="newSym" id="new-symbol-input" placeholder="Enter Stock Symbol">
    <button @click="submitNewSymbol()">Track</button>
    <template v-if="stocks.length">
      <div v-for="(stock, index) in stocks" :key="index">
        <StockCard
          :name="stock.name"
          :sym="stock.symbol"
          :open="stock.recent['1. open']"
          :close="stock.recent['4. close']"
          :high="stock.recent['2. high']"
          :low="stock.recent['3. low']"/>
      </div>
    </template>
  </div>
</template>

<script>
import axios from 'axios'
import StockCard from '@/components/StockCard.vue'

export default {
  name: 'App',
  data () {
    return {
      hasStocks: false,
      symbols: ['GOOG'],
      stocks: [],
      api: 'HY0JP87WH3PG17X6',
      newSym: ''
    }
  },
  components: {
    StockCard
  },
  methods: {
    async getInfo (symbol = '') {
      let sym
      const symInfo = [{
        symbol: '',
        name: ''
      }]

      if (symbol === '') {
        sym = 'GOOG'
      } else {
        sym = symbol
      }

      const url = `https://www.alphavantage.co/query?function=OVERVIEW&symbol=${sym}&apikey=${this.api}`
      const res = await axios.get(url)
      const info = res.data
      symInfo.symbol = info.Symbol
      symInfo.name = info.Name

      return symInfo
    },
    async getStock (symbol = '') {
      let sym
      const symStock = [{
        symbol: '',
        recent: {},
        history: []
      }]

      if (symbol === '') {
        sym = 'GOOG'
      } else {
        sym = symbol
      }

      const url = `https://www.alphavantage.co/query?function=TIME_SERIES_DAILY&symbol=${sym}&output-size=compact&apikey=${this.api}`
      const res = await axios.get(url)
      const dailyStocks = res.data['Time Series (Daily)']
      const recentDate = Object.keys(dailyStocks)[0]
      symStock.symbol = sym
      symStock.recent = dailyStocks[recentDate]
      symStock.history = dailyStocks

      return symStock
    },
    async submitNewSymbol () {
      this.symbols.push(this.newSym)
      localStorage.symbols = this.symbols
      await this.buildStock(this.symbols, true)
      this.newSym = ''
    },
    async buildStock (symbols = [], addNew = false) {
      let data = []
      let company
      let stock

      if (symbols.length === 0) {
        symbols.push('GOOG')
      }

      if (!addNew) {
        for (let i = 0; i < symbols.length; i++) {
          company = await this.getInfo(symbols[i])
          stock = await this.getStock(symbols[i])

          data = { ...company, ...stock }
          this.stocks.push(data)
        }
      } else {
        company = await this.getInfo(symbols[symbols.length - 1])
        stock = await this.getStock(symbols[symbols.length - 1])
        data = { ...company, ...stock }
        this.stocks.push(data)
      }
      this.hasStocks = true
      console.log(this.stocks)
    }
  },
  async mounted () {
    if (localStorage.symbols) {
      const stored = localStorage.symbols.split(',')
      for (let i = 0; i < stored.length; i++) {
        this.symbols.push(stored[i])
      }
    }
    await this.buildStock(this.symbols)
  }
}
</script>

<style lang="scss">
  @import './assets/styles/style.scss';
  #app {
    background-color: green;
  }
</style>
