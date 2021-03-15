<template>
  <div id="app">
    <div id="title-container">
      <h1>Stock Watcher</h1>
    </div>
    <div id="input-container">
      <input type="text" v-model="newSym" id="new-symbol-input" placeholder="Enter Stock Symbol">
      <button @click="submitNewSymbol()">Add Stock</button>
    </div>
    <div id="dashboard">
      <template v-if="stocks.length">
        <div v-for="(stock, index) in stocks" :key="index">
          <StockCard
            :name="stock.name"
            :sym="stock.symbol"
            :open="formatNum(stock.recent['1. open'])"
            :close="formatNum(stock.recent['4. close'])"
            :high="formatNum(stock.recent['2. high'])"
            :low="formatNum(stock.recent['3. low'])"/>
        </div>
      </template>
    </div>
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
      symbols: [],
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
    },
    formatNum (num) {
      return Number(num).toFixed(2)
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

  #title-container {
    h1 {
      font-size: 5rem;
      font-weight: 700;
    }
  }

  #input-container {
    input {
      font-family: 'Open Sans', sans-serif;
      font-size: 1.25rem;
      padding: 1rem 2rem;
      margin-top: 2rem;
      margin-right: 1rem;
    }

    button {
      background: $color-button;
      text-transform: uppercase;
      font-family: 'Open Sans', sans-serif;
      font-size: 1.25rem;
      font-weight: 700;
      padding: 1rem 2rem;
      color: white;
      border: none;
      box-shadow: $shadow-button;
      border-radius: .25rem;
    }
  }

  #dashboard {
    display: grid;
    width: 100%;
    height: auto;
    grid-template-columns: repeat(3, 1fr);
    column-gap: 2rem;
    row-gap: 2rem;
  }

</style>
