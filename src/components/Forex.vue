<template>
  <div class="forex">
    <p>forex</p>
    <b-table striped hover :items="prices" :fields="fields">
      <template #cell(symbol)="data">
        <country-flag :country='data.value[1].country1' size='normal'/><country-flag :country='data.value[1].country2' size='normal'/>{{ data.value[0] }}
      </template>
      <template #cell(bid)="data">
        <span v-if='data.item.bidDif < 0'>&#8595;</span><span v-if='data.item.bidDif > 0'>&#8593;</span>{{ data.value }}
      </template>
      <template #cell(ask)="data">
        <span v-if='data.item.askDif < 0'>&#8595;</span><span v-if='data.item.askDif > 0'>&#8593;</span>{{ data.value }}
      </template>
      <template #cell(spread)="data">
        <span v-if='data.item.spreadDif < 0'>&#8595;</span><span v-if='data.item.spreadDif > 0'>&#8593;</span>{{ data.value }}
      </template>
    </b-table>
    <div class="autoreload" :class="{autoreload_active: autoRef}" @click="autoRef = !autoRef">Live</div>
    <div class="btn" @click="refresh(); onChange()"> <svg id="Layer_1" style="enable-background:new 0 0 128 128;" version="1.1" viewBox="0 0 128 128" xml:space="preserve" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"><g><path d="M96.1,103.6c-10.4,8.4-23.5,12.4-36.8,11.1c-10.5-1-20.3-5.1-28.2-11.8H44v-8H18v26h8v-11.9c9.1,7.7,20.4,12.5,32.6,13.6   c1.9,0.2,3.7,0.3,5.5,0.3c13.5,0,26.5-4.6,37-13.2c19.1-15.4,26.6-40.5,19.1-63.9l-7.6,2.4C119,68.6,112.6,90.3,96.1,103.6z"/><path d="M103,19.7c-21.2-18.7-53.5-20-76.1-1.6C7.9,33.5,0.4,58.4,7.7,81.7l7.6-2.4C9,59.2,15.5,37.6,31.9,24.4   C51.6,8.4,79.7,9.6,98,26H85v8h26V8h-8V19.7z"/></g></svg> refresh</div>
    <button @click="prices[0].bid += 1">click </button>
  </div>
</template>

<script>
import hson from '@/json/data.json'
import CountryFlag from 'vue-country-flag'

export default {
  name: 'Forex',
  components: {CountryFlag},
  data() {
    return {
      autoRef: false,
      fields: ['symbol', 'bid', 'ask', 'spread'],
      prices: null,
      oldPrices: null,
    }
  },
  mounted: function () {
    window.setInterval(() => {
      if (this.autoRef) this.refresh()
    }, 3000)
  },
  methods: {
    refresh() {
      this.onChange();
      this.prices = Object.entries(hson.prices).map((el, index) => el = {
        'symbol': el,
        'bidDif': this.oldPrices ? Number(el[1].bid) - this.oldPrices[index].bid : 0,
        'bid': Number(el[1].bid),
        'askDif': this.oldPrices ? Number(el[1].ask) - this.oldPrices[index].ask : 0,
        'ask':  Number(el[1].ask),
        'spreadDif': this.oldPrices ? Number(el[1].spread) - this.oldPrices[index].spread : 0,
        'spread':  Number(el[1].spread)});
    },
    onChange() {
      this.oldPrices = JSON.parse(JSON.stringify(this.prices));
    },

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .autoreload {
    cursor: pointer;
    margin-left: 50px;
    position: relative;
    display: inline-block;
    &::before {
      content: '';
      background-color: #888;
      border-radius: 20px;
      width: 40px;
      height: 20px;
      position: absolute;
      left: -45px;
      top: 0;
    }
    &::after {
      content: '';
      background-color: #fff;
      border-radius: 50%;
      width: 18px;
      height: 18px;
      position: absolute;
      left: -40px;
      top: 1px;
    }
    &_active {
      &::after {
        left: -28px;
      }
    }
  }
</style>
