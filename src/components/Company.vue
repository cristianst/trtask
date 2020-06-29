<template>
  <div class="company">
    <button @click="subscribe(isin)" :disabled="isSubscribed === true">subscribe</button>
    <button @click="unsubscribe(isin)" :disabled="isSubscribed !== true">unsubscribe</button>
    <span>{{ name }} </span>
    <span>Price: <strong>{{ price }}</strong></span>
  </div>
</template>

<script>
export default {
  name: 'Company',
  props: {
    name: String,
    price: Number,
    isin: String,
    isSubscribed: Boolean,
    onSubscribe: Function,
    onUnsubscribe: Function,
  },
  methods: {
    subscribe(isin){
      this.$emit('onSubscribe', isin);
    },
    unsubscribe(isin){
      this.$emit('onUnsubscribe', isin);
    }
  }
}
</script>

<style scoped>
.company {
  margin: 10px auto;
}
</style>
