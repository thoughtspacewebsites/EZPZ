<template>
      <div class="ezpz-full-screen-icon-container">
        <div class="ezpz-full-screen-icon" v-bind:class="type">
          <span class="fa" v-bind:class="icon"></span>
        </div>
      </div>

</template>
<script>

    export default {
	name: 'FullScreenIcon',
        props: ['type', 'icon'],
        mixins: [],
        data: function(){
            return {

            }
        },
        methods: {

        },
        computed: {

        },
        mounted: function(){
          this.$el.parentNode.removeChild(this.$el);
          document.body.appendChild(this.$el);
        }
    }
</script>
<style lang="scss" scoped>
    .ezpz-full-screen-icon-container{
      position:fixed;
      top:0;
      left:0;
      width:100%;
      height:100vh;
      display:flex;
      align-items:center;
      justify-content:center;
      z-index:10000000000;
    }
    .ezpz-full-screen-icon{
      width:300px;
      height:300px;
      display:flex;
      align-items:center;
      justify-content:center;
    }
    .ezpz-full-screen-icon .fa{
      font-size:150px;
      border-radius:50%;
      width:100%;
      height:100%;
      text-align:center;
      background:$dark-bg-color;
      color:$light-text-color;
      display:flex;
      align-items:center;
      justify-content:center;
    }
    .ezpz-full-screen-icon.success .fa{
      background:$form-success;
      color:$light-text-color;
    }
    .ezpz-full-screen-icon.warn .fa{
      background:$form-warn;
      color:$light-text-color;
    }
    .ezpz-full-screen-icon.info .fa{
      background:$form-info;
      color:$light-text-color;
    }
    .ezpz-full-screen-icon.error .fa{
      background:$form-error;
      color:$light-text-color;
    }
</style>
