<template>
  <div class="payment-unsuccessful-details body-2 pa-4">
    <h4>Payment Unsuccessful</h4>

    <p>This filing is pending payment. The payment appears to have been unsuccessful for some
      reason.</p>

    <p>You may continue this filing by selecting "Retry Payment".</p>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'

@Component({})
export default class PaymentUnsuccessful extends Vue {}
</script>

<style lang="scss" scoped>
p:first-of-type {
  padding-top: 0.75rem;
}

p {
  margin-bottom: 0.5rem !important;
}
</style>
