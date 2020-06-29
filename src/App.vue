<template>
  <div id="app">
    <div class="companies">
      <div v-for="company in companies" :key="company.isin">
        <Company 
          v-bind:name="company.name"
          v-bind:price="company.price"
          v-bind:isin="company.isin"
          v-bind:isSubscribed="company.isSubscribed"
          @onSubscribe="subscribeToInstrument"
          @onUnsubscribe="unsubscribeFromInstrument"
        />
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue';
import Company from './components/Company';

export default {
  name: 'App',
  components: {
    Company
  },
  data(){
    return {
      companies: [{
        name: 'Google Inc',
        isin: 'US38259P5089',
        isSubscribed : false,
        price: null
      }, {
        name: 'Apple Inc',
        isin: 'US0378331005',
        isSubscribed : false,
        price: null
      }, {
        name: 'Netflix Inc',
        isin: 'US64110L1061',
        isSubscribed : false,
        price: null
      }]
    }
  },
  methods: {
    updateCompanyFromState(isin, newState){
      const index = this.companies.findIndex(company => company.isin === isin);
      const current = this.companies[index];

      Vue.set(this.companies, index, { ...current, ...newState});

    },
    subscribeToInstrument(isin){
      
      const message = {
        subscribe: isin
      };

      this.connection.send(JSON.stringify(message));
      this.updateCompanyFromState(isin, { isSubscribed: true });
    },
    unsubscribeFromInstrument(isin){
      const message = {
        unsubscribe: isin
      };

      this.connection.send(JSON.stringify(message));
      this.updateCompanyFromState(isin, { isSubscribed: false });
    },
    
  },
  created(){
    this.connection = new WebSocket('ws://159.89.15.214:8080/');

    this.connection.onclose = () => {
      // show some user feedback
    } 

    this.connection.onmessage = () => {
      const { data } = event;
      const { isin, price } = JSON.parse(data);

      const priceRoundedToTwoDecimals = Math.round((price + Number.EPSILON) * 100) / 100

      this.updateCompanyFromState(isin, { price: priceRoundedToTwoDecimals })
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
  margin-top: 60px;
}
</style>
