<template>
  <section class="ezpz-quantity-modal-container">
    <div class="ezpz-quantity-backdrop"></div>
    <div class="ezpz-quantity-modal">
      <div class="quantity-header">
        <span>Quantity:</span>
        <span class="quantity">
          <span v-html="quantity" v-if="quantity"></span>
          <span v-if="!quantity">--</span>
        </span>
      </div>
      <transition name="scale">
          <notification type="error" v-if="invalidQuantity">Please enter a valid quantity!</notification>
      </transition>
      <div class="pin-container">
        <pin-pad v-model="quantity"></pin-pad>
      </div>
      <div class="button full-width confirm-button" v-on:click="setQuantity()">Confirm</div>
      <div class="cancel text-flash" v-on:click="cancelQuantitySet()">
        CANCEL
      </div>
    </div>
  </section>
</template>
<script>
import Notification from './notification.vue';
import PinPad from './pin-pad.vue';

export default {
  name: 'QuantityModal',
  components: {
    PinPad, Notification
	},
	props: ['value'],
  mixins: [],
	data: function(){
	    return {
        quantity: 0,
        invalidQuantity: false
	    }
	},
  computed: {

  },
	methods: {
    setQuantity: function(){
      this.invalidQuantity = false;
      if(this.quantity){
        this.$emit('input', this.quantity);
      }
      else{
        this.invalidQuantity = true;
      }
    },
    cancelQuantitySet: function(){
      this.$emit('cancel');
    }
  },
  created: function(){
    this.quantity = this.value;
  }
}
</script>
<style scoped lang="scss">
  .ezpz-quantity-modal-container{

  }
  .ezpz-quantity-backdrop{
    position:fixed;
    top:0;
    left:0;
    width:100%;
    height:100vh;
    z-index:100000000;
    background:rgba(255,255,255,.75);
  }
  .ezpz-quantity-modal{
    width:500px;
    position:fixed;
    top:50%;
    left:50%;
    z-index:1000000000;
    transform:translateX(-50%) translateY(-50%);
  }
  .ezpz-quantity-modal .pin-container,
  .ezpz-quantity-modal .quantity-header{
    background:$dark-bg-color;
    border-radius:25px;
    overflow:hidden;
  }
  .ezpz-quantity-modal .quantity-header{
    text-align:center;
    font-size:25px;
    text-transform:uppercase;
    margin-bottom:30px;
    display:flex;
    align-items:center;
    justify-content:center;
    padding-left:30px;
  }
  .ezpz-quantity-modal::v-deep .number {
  color:$light-text-color;
  border-bottom-color:rgba(255,255,255,.1);
  border-right-color:rgba(255,255,255,.1);
  }
  .ezpz-quantity-modal::v-deep .number:nth-child(1),
  .ezpz-quantity-modal::v-deep .number:nth-child(2),
  .ezpz-quantity-modal::v-deep .number:nth-child(3){
    border-top-color:1px solid rgba(255,255,255,.1);
  }
  .ezpz-quantity-modal::v-deep .number:nth-child(1),
  .ezpz-quantity-modal::v-deep .number:nth-child(4),
  .ezpz-quantity-modal::v-deep .number:nth-child(7),
  .ezpz-quantity-modal::v-deep .number:nth-child(10){
    border-left-color:rgba(255,255,255,.1);
  }
  .quantity{
    background:rgba(255,255,255,.2);
    color:$light-text-color;
    font-size:30px;
    font-weight:700;
    padding:30px 50px;
    margin-left:auto;
  }
  .confirm-button{
    position:relative;
    top:30px;
    padding:25px;
    font-size:20px;
    border-radius:25px;
  }
  .cancel{
    color:$form-error;
    font-size:20px;
    font-weight:900;
    text-align:center;
    margin-top:50px;
  }
</style>
