<template>
 <div class="stock-card">
  <div class="graphic positive"></div>
  <div class="info">
    <div class="company-info">
      <h1 class="company-name">{{ name }}</h1>
      <h4 class="company-symbol">{{ sym }}</h4>
    </div>
    <div class="current"></div>
    <div class="stats">
      <p>Open <span>{{ open }}</span></p>
      <p>High <span>{{ high }}</span></p>
      <p>Low <span>{{ low }}</span></p>
    </div>
  </div>
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
      type: [Number, String],
      default: 2085.00
    },
    close: {
      type: [Number, String],
      default: 2061.92
    },
    high: {
      type: [Number, String],
      default: 2090.26
    },
    low: {
      type: [Number, String],
      default: 2047.55
    }
  }
}
</script>

<style lang="scss" scoped>
  @import '../assets/styles/style.scss';

  .stock-card {
    width: 100%;
    height: auto;
    display: grid;
    grid-template-columns: 1.5fr 4fr;
    background-color: white;
    box-shadow: $shadow-card;

    .info {
      display: grid;
      grid-template-rows: 1fr 1fr 1.75rem;
      padding: 1rem 2rem;

      .company-info {

        .company-name {
          display: block;
          overflow: hidden;
          max-width: 18ch;
          white-space: nowrap;
          text-overflow: ellipsis;
          font-weight: 800;
          text-transform: uppercase;
        }

        .company-symbol {
          font-size: 1.25rem;
          font-weight: 400;
          color: $color-title;
          text-transform: uppercase;
        }
      }

      .stats {
        display: grid;
        grid-template-columns: repeat(3, 1fr);

        p {
          font-weight: 400;
          text-transform: uppercase;
          color: $color-title;

          span {
            font-weight: 600;
            color: $color-typeNumber;
          }
        }
      }
    }
  }

  .positive {
    background: linear-gradient(0deg, $color-greenDark, $color-greenLight);
  }

  .negative {
    background: linear-gradient(0deg, $color-redDark, $color-redLight);
  }
</style>
