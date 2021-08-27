<template>
    <div class="ezpz-ls-container" v-bind:class="{fullscreen: fullscreen, floating: size == 'floating'}">
      <div class="ezpz-loading-spinner" v-bind:class="size">
          <img v-bind:src="'./loading'+compType+'.gif'" alt="Loading, Please Wait..." title="Loading, Please Wait..." />
          <h2 v-if="size != 'small' && size != 'medium'">{{text || "Loading, Please Wait..."}}</h2>
      </div>
    </div>
</template>
<script>
    export default {
	name: 'LoadingSpinner',
        props: ['type', 'size', 'fullscreen', 'text'],
        mixins: [],
        data: function(){
            return {

            }
        },
        methods: {

        },
        computed: {

            compType: function(){
                if(this.type == 'dark' || this.size == 'floating'){
                    return '-dark';
                }
                else{
                    return '';
                }
            }
        }
    }
</script>
<style lang="scss" scoped>
    .ezpz-ls-container.fullscreen{
      background:rgba(255,255,255,1);
      position:fixed;
      top:0;
      left:0;
      z-index:100000000000000;
      width:100%;
      height:100vh;
      display:flex;
      align-items:center;
      justify-content:center;
    }
    .ezpz-ls-container.floating{
      position:fixed;
      bottom:30px;
      right:30px;
      z-index:10000000000;
      display:flex;
      align-items:center;
      justify-content:center;
      border-radius:200px;
      padding:10px 40px;
      color:$light-text-color;
      background:$dark-bg-color;
    }
    .ezpz-loading-spinner{
        display:block;
        margin:auto;
        text-align:center;
        margin-top:150px;
        margin-bottom:150px;
        width:100%;
    }
    .ezpz-ls-container.floating .ezpz-loading-spinner{
      margin:0 !important;
    }
    .ezpz-loading-spinner.small,
    .ezpz-loading-spinner.medium{
        margin-top:15px;
        margin-bottom:15px;
    }

    .ezpz-loading-spinner.small img{
        width:50px;
    }

    .ezpz-loading-spinner.medium img{
        width:100px;
    }

    .ezpz-loading-spinner img{
        max-width:150px;
        width:100%;
    }
    .ezpz-ls-container.floating img{
      max-width:50px;
    }
    .ezpz-loading-spinner h2{
        font-size:25px;
        margin-top:20px;
        color:$main-black;
    }
    .ezpz-ls-container.floating .ezpz-loading-spinner{
      display:flex;
      align-items:center;
      justify-content:center;
    }
    .ezpz-ls-container.floating h2{
      color:$light-text-color;
      font-size:14px;
      margin:0;
    }
</style>
