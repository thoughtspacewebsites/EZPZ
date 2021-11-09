<template>
    <div class="ezpz-field-wrap">
        <label v-bind:for="fieldName" v-if="label">{{label}} <span class="req" v-if="required">*</span></label>
        <div class="input-container" v-bind:class="{'has-leading-icon': leadingIcon}">

          <div v-if='leadingIcon'>
            <span class="leading-icon fa" v-bind:class="leadingIcon"></span>
          </div>

          <input v-bind:class="{'no-keyboard': noKeyboard}" v-bind:type="type" v-if="type=='text' || type=='email'" v-bind:name="fieldName" v-bind:id="fieldName" @input="catchUpdateEvent" v-bind:value="localVal" v-bind:disabled="disabled" v-bind:maxlength="maxlength" />
          
          <input v-bind:class="{'no-keyboard': noKeyboard}" v-bind:type="type" v-if="type=='number'" v-bind:name="fieldName" v-bind:id="fieldName" @input="catchUpdateEvent" v-bind:value="localVal" v-bind:disabled="disabled" v-bind:maxlength="maxlength" />

          <textarea v-bind:name="fieldName" v-if="type=='textarea'" v-bind:id="fieldName" @input="catchUpdateEvent" v-bind:value="localVal" v-bind:disabled="disabled"></textarea>

          <div class="toggle-container" v-if="type=='toggle'" v-bind:class="{active: isToggleActive, light: lightMode}">
            <div class="slider"></div>
            <div class="label off" v-on:click="changeFieldValue(false)">{{offLabel}}</div>
            <div class="label on" v-on:click="changeFieldValue(true)">{{onLabel}}</div>
          </div>

          <div class="select-container" v-if="type=='select'">
            <div class="selected-placeholder" v-bind:class="{'is-selected': localVal}" v-on:click="toggleSelectVisibility()">
              <span class="fa fa-ban" v-if="localVal" v-on:click.stop="changeFieldValue(false);"></span>
              <p v-if="localVal" v-html="localVal.label"></p>
              <p v-if="!localVal" class="placeholder-text">
                Please select a value
              </p>
              <span class="fa fa-chevron-down"></span>
            </div>
            <transition name='zoom'>
              <div class="select-list" v-bind:class="'select-list-'+id" v-if="selectVisible">
                <div class="background" v-on:click="toggleSelectVisibility()"></div>
                <div class="options-container">
                  <div class="title">
                    {{label}}
                  </div>
                  <div v-if="options.length > 20" class="options-search">
                    <span class="fa fa-search"></span>
                    <input type="text" v-model="selectSearch" placeholder="Search..." />
                  </div>
                  <div class="options">
                    <div class="option" v-for="option in filteredOptions" v-bind:class="{selected: isEqual(option, localVal)}" v-on:click="changeFieldValue(option);toggleSelectVisibility()" v-html="option.label"></div>
                  </div>
                </div>
              </div>
            </transition>
          </div>

          <transition name="scale">
            <loading-spinner size="small" v-if="loading" class="field-loading"></loading-spinner>
          </transition>

          <slot></slot>
        </div>
        <transition name="scale">
            <notification type="error" v-if="error">{{error}}</notification>
        </transition>
    </div>
</template>
<script>
    import Notification from './notification.vue';
    import LoadingSpinner from './loading-spinner.vue';

    var _ = require('lodash');
    export default {
	name: 'FormField',
        components: {
      		Notification, LoadingSpinner
      	},
        props: ['type', 'field', 'value', 'label', 'error', 'onLabel', 'offLabel', 'useLabelsAsValue', 'loading', 'required', 'disabled', 'options', 'maxlength', 'leadingIcon', 'lightMode', 'noKeyboard'],
        data: function(){
            return {
                id: false,
                formSettings: this.$root.$data.formSettings,
                isToggleActive: false,
                localVal: this.value ? this.value : "",
                selectVisible: false,
                selectIsMoved: false,
                selectSearch: ""
            }
        },
        computed: {
          filteredOptions: {
            get: function(){
              if(this.options){
                if(!this.selectSearch || this.selectSearch == ""){
                  return this.options;
                }
                else{
                  var that = this;
                  return this.options.filter(function(option){
                    return option.label.toLowerCase().includes(that.selectSearch.toLowerCase());
                  });
                }
              }
              return false;
            },
            set: function(){

            }
          },
          fieldName: function(){
              return "ezpz-forms-"+this.field
          },
        },
        methods: {
            changeFieldValue(toUpdate) {
              var emitVal = "";
              if(this.type == "toggle" && this.useLabelsAsValue == "true"){
                emitVal = emitVal == toUpdate ? this.offLabel : this.onLabel;
              }
              else{
                emitVal = toUpdate;
              }
              
              this.$emit("input", emitVal);
              this.localVal = emitVal;
              this.$nextTick(function(){
                this.testIsToggleActive();
              })

            },
            catchUpdateEvent: function(event){
              this.changeFieldValue(event.target.value);
            },
            testIsToggleActive: function(){
              if(this.useLabelsAsValue != "true"){
                this.isToggleActive = this.localVal;
              }
              else{
                this.isToggleActive = (this.localVal == this.onLabel);
              }
            },
            toggleSelectVisibility: function(){
              this.selectVisible = !this.selectVisible;
              var that = this;
              this.$nextTick(function(){
                that.moveSelect();
              });

            },
            isEqual: function(a, b){
              return _.isEqual(a,b);
            },
            moveSelect: function(){
              if(this.type == 'select' && document.getElementsByClassName('select-list-'+this.id)[0] && !this.selectIsMoved){
                this.selectIsMoved = true;
                var content = document.getElementsByClassName('select-list-'+this.id)[0];
                var body = document.getElementsByClassName('oakmont-bakery-pos')[0];
                content.parentNode.removeChild(content);
                body.appendChild(content);
              }
            }
        },
        mounted: function(){
          if(this.type == 'toggle'){
            if(!this.value && this.useLabelsAsValue){
              this.$emit("input", this.offLabel);
            }
            this.testIsToggleActive();
          }
          this.id = this._uid
          this.moveSelect();

        },
        watch: {
          value: {
            handler: function(val){
            if(val != this.localVal){
              this.localVal = val;
            }
          }
          }
        }
    }
</script>
<style lang="scss" scoped>

    .ezpz-field-wrap{
        margin-top:15px;
        margin-bottom:15px;
    }

    .dashboard-widget .ezpz-field-wrap{
      margin-top:5px;
      margin-bottom:5px;
    }
    .input-container{
      position:relative;
    }

    label{
        margin-left:15px;
        display:block;
        margin-bottom:5px;
    }

    label .req{
      color:$form-error;
    }

    .leading-icon{
      background:$dark-bg-color;
      color:$light-text-color;
      position:absolute;
      left:0;
      top:0;
      bottom:0;
      height:100%;
      width:70px;
      font-size:20px;
      display:flex;
      align-items:center;
      justify-content:center;
      border-top-left-radius:30px;
      border-bottom-left-radius:30px;
    }

    .has-leading-icon input{
      padding-left:75px;
    }

    input,
    textarea,
    .selected-placeholder{
        background:$mid-bg-color;
        border:5px solid $mid-bg-color;
        border-radius:30px;
        padding:10px 30px;
        height:auto;
        display:block;
        width:100%;
        transition:all .2s;
        resize:none;
    }
    textarea{
      min-height:200px;
    }

    input:focus,
    textarea:focus{
        background:$light-bg-color;
        border:5px solid $mid-bg-color;
        outline:none;
    }
    .selected-placeholder{
      position:relative;
    }
    .selected-placeholder.is-selected{
      padding-left:50px;
    }
    .selected-placeholder p{
      color:$dark-text-color;
      margin:0;
      text-align:left;
    }
    .selected-placeholder p.placeholder-text{
      color:rgba(0,0,0,.3);
      font-style:italic;
    }
    .selected-placeholder .fa{
      position:absolute;
      top:50%;
      transform:translateY(-50%);
      width:40px;
      height:40px;
      border-radius:50%;
      line-height:40px;
      text-align:center;
    }
    .selected-placeholder .fa-chevron-down{
      right:3px;
      background:$dark-bg-color;
      color:$light-text-color;
    }
    .selected-placeholder .fa-ban{
      left:3px;
      background:$form-error;
      color:$light-text-color;
    }
    .select-list{
      position:fixed;
      top:0;
      left:0;
      z-index:30000000000;
      width:100%;
      height:100vh;
      display:flex;
      align-items:center;
      justify-content: center;
    }
    .select-list .title{
      font-size:25px;
      background:$dark-bg-color;
      color:$light-text-color;
      padding:30px;
    }
    .select-list .background{
      position:absolute;
      top:0;
      left:0;
      width:100%;
      height:100vh;
      background:rgba(255,255,255,.75);
      z-index:2;
    }
    .select-list .options-search{
      position:relative;
    }
    .select-list .options-search .fa{
      position:absolute;
      left:20px;
      top:50%;
      transform:translateY(-50%);
    }
    .select-list .options-search input{
      background:$light-bg-color;
      border-radius:0;
      padding:20px;
      padding-left:50px;
    }
    .select-list .options-container{
      position:relative;
      width:400px;
      z-index:5;
      background:$light-bg-color;
      color:$dark-text-color;
      font-size:20px;
      border-radius:10px;
      overflow:hidden;
    }
    .select-list .options{
      max-height:500px;
      overflow-y:scroll;
      background:
        linear-gradient(#eaeaea 33%, rgba(234,234,234, 0)),
        linear-gradient(rgba(234,234,234, 0), #eaeaea 66%) 0 100%,
        radial-gradient(farthest-side at 50% -40%, rgba(0,0,0, 0.7), rgba(0,0,0,0)),
        radial-gradient(farthest-side at 50% 140%, rgba(0,0,0, 0.7), rgba(0,0,0,0)) 0 100% ;
      background-color: #eaeaea;
      background-repeat: no-repeat;
      background-attachment: local, local, scroll, scroll;
      background-size: 100% 70px, 100% 70px, 100% 34px, 100% 34px;
    }
    .select-list .option{
      padding:20px 30px;
      border-bottom:1px solid rgba(0,0,0,.1);
      transition:all .2s;
    }
    .select-list .option.selected{
      background:$form-success;
      color:$light-text-color;
    }
    .select-list .option:last-of-type{
      border-bottom:none;
    }
    .field-loading{
      position:absolute;
      top:50%;
      right:0px;
      transform:translateX(-50%) translateY(-50%);
    }
    .dashboard-widget input,
    .dashboard-widget .selected-placeholder{
      background:$light-bg-color;
      border:5px solid $light-bg-color;
    }
    .dashboard-widget input{
      padding:5px 15px;
    }

    .toggle-container{
      background:rgba($mid-bg-color, .2);
      color:$dark-text-color;
      border-radius:30px;
      overflow:hidden;
      position:relative;
    }
    .toggle-container.light{
      background:rgba($light-bg-color, .2);
    }
    .toggle-container .label{
      width:50%;
      margin-left:-2px;
      margin-right:-2px;
      text-align:center;
      padding:20px;
      display:inline-block;
      vertical-align:middle;
      position:relative;
      z-index:5;
      font-weight:700;
      text-transform:uppercase;
      font-size:12px;
      transition:all .2s;
      -webkit-transition:all .2s;
    }
    .toggle-container:not(.active) .label.off{
      color:$light-text-color;
    }
    .toggle-container.active .label.on{
      color:$light-text-color;
    }
    .toggle-container.light .label{
      color:$dark-text-color !important;
    }
    .toggle-container .slider{
      background:$dark-bg-color;
      width:50%;
      height:100%;
      position:absolute;
      z-index:1;
      left:0;
      top:0;
      bottom:0;
      transition:all .2s;
      -webkit-transition:all .2s;
    }
    .toggle-container.light .slider{
      background:$light-bg-color;
    }
    .toggle-container.active .slider{
      left:50%;
    }
</style>
