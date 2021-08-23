<template>
    <div class="ezpz-notification" v-bind:data-type="type">
        <div class="ezpz-not-icon">
          <span class="fa fa-info" v-if="!type || type == 'info'"></span>
          <span class="fa fa-check" v-if="type == 'success'"></span>
          <span class="fa fa-exclamation-triangle" v-if="type == 'error'"></span>
          <span class="fa fa-exclamation-triangle" v-if="type == 'warn'"></span>
        </div>
        <div class="ezpz-not-content">
            <slot></slot>
        </div>
    </div>
</template>
<script>
    export default {
	name: 'Notification',
        props: ['type'],
        data: function(){
            return {

            }
        },
        methods: {

        }
    }
</script>
<style lang="scss" scoped>
    .ezpz-notification{
        position:relative;
        margin-top:10px;
        margin-bottom:10px;
        position:relative;
        padding:15px 30px 15px 65px;
        background:$main-black;
        color:$light-text-color;
        text-shadow:none;
        font-size:15px;
        text-align:left;
        border-radius:30px;
        line-height:150%;
    }

    .ezpz-notification[data-type=success]{
        background:$form-success;
    }

    .ezpz-notification[data-type=error]{
        background:$form-error;
    }

    .ezpz-notification[data-type=warn]{
        background:$form-warn;
    }

    .ezpz-notification[data-type=info]{
        background:$form-info;
    }

    .ezpz-not-icon{
     position:absolute;
     left:8px;
     top:50%;
     height:40px;
     background:$light-bg-color;
     color:rgba(0,0,0,.15) !important;
     font-size:20px;
     width:40px;
     border-radius:30px;
     text-align:center;
      transform:translateY(-50%);
      -webkit-transform:translateY(-50%);
      -ms-transform:translateY(-50%);
    }

    .ezpz-notification[data-type=success] .ezpz-not-icon .fa{
        color:$form-success;
    }

    .ezpz-notification[data-type=error] .ezpz-not-icon .fa{
        color:$form-error;
    }

    .ezpz-notification[data-type=warn] .ezpz-not-icon .fa{
        color:$form-warn;
    }

    .ezpz-notification[data-type=info] .ezpz-not-icon .fa{
        color:$form-info;
    }

    .ezpz-not-icon .fa{
        position:absolute;
        top:50%;
        left:50%;
        transform:translateX(-50%) translateY(-50%);
        -webkit-transform:translateX(-50%) translateY(-50%);
        -ms-transform:translateX(-50%) translateY(-50%);
    }
</style>
