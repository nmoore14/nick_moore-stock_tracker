<template>
 <div>
  <h1 id="company-name">{{ props.name }}</h1>
  <h4>{{ props.sym }}</h4>
  <p>
    Open: {{ props.open }}<br />
    High: {{ props.high }} - Low: {{ props.low }}<br />
    Close: {{ props.close }}
  </p>
 </div>
</template>

<script>
export default {
  name: 'StockCard',
  props: {
    name: {
      type: String,
      default: 'Alphabet, Inc.'
    },
    sym: {
      type: String,
      default: 'GOOG'
    },
    open: {
      type: Number,
      default: 2085.00
    },
    close: {
      type: Number,
      default: 2061.92
    },
    high: {
      type: Number,
      default: 2090.26
    },
    low: {
      type: Number,
      default: 2047.55
    },
    current: {
      type: Number,
      default: (this.low + this.high) / 2
    }
  }
}
</script>

<style lang="scss" scoped>
  #company-name {
    overflow: hidden;
    max-width: 20ch;
  }
</style>
