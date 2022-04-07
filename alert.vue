<template>

  <section class="ezpz-alert" v-on:click.stop.prevent="" v-bind:class="{visible: message || shown}">
    <div class="ezpz-alert-container">
      <transition name="fade">
        <div class="alert-overlay" v-on:click="dismissAlert()" v-if="message || shown"></div>
      </transition>
      <transition name="fadeUp">
        <div class="alert" v-if="message || shown" v-bind:class="type">
          <span v-html="message"></span>
          <slot></slot>
          <div class="dismiss-alert">
            <span class="fa fa-times" v-on:click="dismissAlert()"></span>
          </div>
          <div class="center-contents confirm-container" v-if="hasConfirm">
            <div class="button" v-on:click="confirm()">OK</div>
            <div class="button small" v-on:click="dismissAlert()">Cancel</div>
          </div>
        </div>
      </transition>
    </div>

  </section>

</template>
<script>
export default {
  name: 'Alert',
  components: {

	},
	props: [ "hasConfirm", "message", "type", "shown"],
  mixins: [],
	data: function(){
	    return {
        dismissing: false,
	    }
	},
	computed: {

	},
	methods: {
    dismissAlert: function(){
      this.dismissing = true;
      this.$emit('dismiss');
      var that = this;
      setTimeout(function(){
        that.dismissing = false;
      },500)
    },
    confirm: function(){
      this.$emit('confirm');
      this.dismissAlert();
    }
	},
  mounted: function(){

    //NEW WAY
    // this.id = this._uid;
    // this.$root.$el.append(this.$el);


    //OLD WAY
    // this.$el.parentNode.removeChild(this.$el);
    // document.getElementById('oakmont-bakery-pos').appendChild(this.$el);
  },
}
</script>
<style scoped lang="scss">
  .ezpz-alert{
    display:none;
  }
  .ezpz-alert.visible{
    display:block;
    position:fixed;
    top:0;
    left:0;
    right:0;
    bottom:0;
    width:100%;
    height:100vh;
    z-index:100000000;
  }
  .ezpz-alert-container {
    display: flex;
    align-items: center;
    justify-content: center;
    width:100%;
    height:100vh;

  }

  .alert-overlay{
    position:fixed;
    left:0;
    top:0;
    right:0;
    bottom:0;
    z-index:1000000;
    width:100%;
    height:100vh;
    background:rgba($dark-bg-color, .5);
    cursor:pointer;
  }
  .alert{
    position:relative;
    z-index:15000000;
    width:600px;
    height:auto;
    padding:60px;
    font-size:20px;
    border-radius:0;
    border:none;
    background:$dark-bg-color;
    color:$light-text-color;
  }

  .alert.error{
    background:$form-error;
  }
  .alert.success{
    background:$form-success;
  }
  .alert.warn{
    background:$form-warn;
  }
  .alert.info{
    background:$form-info;
  }

  .dismiss-alert{
    position:absolute;
    top:0px;
    right:0px;
    height:60px;
    width:60px;
    color:rgba(0,0,0,.15);
    background:rgba(0,0,0,.15);
    font-size:25px;
    line-height:60px;
    text-align:center;
    cursor:pointer;
  }
  .confirm-container{
    margin-top:15px;
  }
</style>
