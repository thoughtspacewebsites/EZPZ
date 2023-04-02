<template>
    <div class="ezpz-field-wrap" v-bind:class="{'has-length-counter': maxLength}">
        <label v-bind:for="fieldName" v-if="label" v-html="parsedLabel"></label>
        <div class="input-container" v-bind:class="{'has-leading-icon': leadingIcon}">

          <div v-if='leadingIcon' v-on:click="iconClick">
            <span class="leading-icon fa" v-bind:class="leadingIcon"></span>
          </div>

          <input ref="inputEl" v-bind:class="{'no-keyboard': noKeyboard}" v-bind:type="type" v-if="type=='text' || type=='email' || type=='password' || type=='tel'" v-bind:name="fieldName" v-bind:id="fieldName" @input.stop="catchUpdateEvent" v-bind:value="localVal" v-bind:disabled="disabled" v-bind:maxlength="maxLength" v-bind:minlength="minLength" v-bind:placeholder="placeholder" />
          
          <input ref="inputEl" v-bind:class="{'no-keyboard': noKeyboard}" v-bind:type="type" v-if="type=='number'" v-bind:name="fieldName" v-bind:id="fieldName" @input.stop="catchUpdateEvent" v-bind:value="localVal" v-bind:disabled="disabled" v-bind:maxlength="maxLength" v-bind:minlength="minLength" v-bind:placeholder="placeholder" />

          <textarea ref="inputEl" v-bind:name="fieldName" v-if="type=='textarea'" v-bind:id="fieldName" @input.stop="catchUpdateEvent" v-bind:value="localVal" v-bind:disabled="disabled" v-bind:maxlength="maxLength" v-bind:minlength="minLength" v-bind:placeholder="placeholder"></textarea>

          <input ref="inputEl" type="checkbox" v-bind:name="fieldName" v-if="type=='checkbox'" v-bind:id="fieldName" @input.stop="catchUpdateEvent" v-bind:checked="localVal" v-bind:disabled="disabled" />

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



          <div class="image-upload-container" v-if="type=='image'" v-bind:class="{'is-selected': localVal, 'error': error}">
            <input type="file" v-bind:name="fieldName" v-bind:id="fieldName" v-bind:disabled="disabled" v-on:input.stop="catchUpdateEvent" accept="image/*" ref="fileInput" />
            <div class="image-upload-placeholder">
              <span class="fa fa-upload"></span>
              <p v-if="!localVal" class="placeholder-text">
                Please select an image
              </p>
              <img v-if="localVal && localVal.base64" v-bind:src="localVal.base64" />
              <img v-else-if="localVal && localVal.length" v-bind:src="localVal" />
              <div class="icon-container">
                <span class="fa fa-pencil" v-if="localVal" v-on:click.stop="selectFile()"></span>
                <span class="fa fa-ban" v-if="localVal" v-on:click.stop="changeFieldValue(false);"></span>
              </div>
            </div>
          </div>

          <transition name="scale">
            <loading-spinner size="small" v-if="loading" class="field-loading"></loading-spinner>
          </transition>

          <div v-if="maxLength && !disabled" class="length-counter" v-bind:class="{warn: localVal.length + 15 > maxLength, error: localVal.length == maxLength}">
            <span class="current-length">{{localVal.length}}</span>
            <span class="max-length">/{{maxLength}}</span>
          </div>

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
        props: [
          'type', 
          'field', 
          'value', 
          'label', 
          'error', 
          'onLabel', 
          'offLabel', 
          'useLabelsAsValue', 
          'loading', 
          'required', 
          'disabled', 
          'options', 
          'maxLength', 
          'minLength',
          'placeholder',
          'leadingIcon', 
          'lightMode', 
          'noKeyboard', 
          'focusOnCreate'
        ],
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
          parsedLabel: function(){
            return this.label + (this.required ? "<span class='req'>*</span>" : '');
          },
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
              if(this.type == 'checkbox'){
                this.changeFieldValue(event.target.checked);
              }
              else if(event.srcElement && event.srcElement.type == 'file' && event.srcElement.accept == 'image/*'){
                var base64 = "";
                var reader = new FileReader();
                reader.readAsDataURL(event.target.files[0]);
                reader.onload = function () {
                  base64 = reader.result;
                  var filename = event.target.files[0].name;
                  var imageData = {
                    filename: filename,
                    base64: base64
                  }
                  this.changeFieldValue(imageData);
                }.bind(this);
                reader.onerror = function (error) {
                  console.log('Error reading image: ', error);
                };
              }
              else{
                this.changeFieldValue(event.target.value);
              }
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
                var body = document.getElementsByTagName('body')[0];
                content.parentNode.removeChild(content);
                body.appendChild(content);
              }
            },
            iconClick: function(){
              this.$emit("icon-click");
            },
            selectFile: function(){
              this.$refs.fileInput.click();
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

          if(this.focusOnCreate){
            var that = this;
            this.$nextTick(function(){
              that.$refs.inputEl.focus();
            })
          }

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
      padding-left:75px !important;
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
        &:disabled{
          cursor:not-allowed;
          color:rgba(0,0,0,.5);
        }
    }

    .has-length-counter input,
    .has-length-counter textarea{
      padding-right:75px !important;
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

    input[type=checkbox]{
      width:auto;
      text-align:center;
    }

    input[type="checkbox"] {
      /* Add if not using autoprefixer */
      -webkit-appearance: none;
      /* Remove most all native input styles */
      appearance: none;
      /* For iOS < 15 */
      background-color:$light-bg-color;
      /* Not removed via appearance */
      margin: 0;

      font: inherit;
      color: $form-success;
      width: 25px;
      height: 30px;
      border-width:3px !important;
      border-radius: 5px;
      display: grid;
      place-content: center;
    }

    input[type="checkbox"]::before {
      content: "";
      width: 1em;
      height: 1em;
      clip-path: polygon(14% 44%, 0 65%, 50% 100%, 100% 16%, 80% 0%, 43% 62%);
      transform: scale(0);
      transform-origin: bottom left;
      transition: 120ms transform ease-in-out;
      box-shadow: inset 1em 1em $form-success;
      /* Windows High Contrast Mode */
      background-color: $form-success;
    }

    input[type="checkbox"]:checked{
      border:3px solid $form-success;
    }
    input[type="checkbox"]:checked::before {
      transform: scale(1);
    }

    input[type="checkbox"]:focus {
      border:3px solid $form-info !important;
    }

    input[type="checkbox"]:disabled {
      --form-control-color: var($form-error);

      color: $form-error;
      cursor: not-allowed;
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

    .image-upload-container{
      position:relative;
      width:100%;
      height:350px;
      overflow:hidden;
      border-radius:10px;
      background:$light-bg-color;
      color:rgba(0,0,0,.5);
      font-weight:700;
      display:flex;
      align-items:center;
      justify-content: center;
      flex-direction:column;
      transition:all .2s;
      -webkit-transition:all .2s;
      border:5px dashed $mid-bg-color;
      cursor:pointer;
      &.is-selected{
        border-color:transparent;
        &:after{
          content:'';
          position:absolute;
          bottom:0;
          left:0;
          width:100%;
          height:100%;
          background:rgba(0,0,0,.5);
          z-index:1;
        }
      }
      &.error{
        border-color:$form-error;
      }
    }
    .image-upload-container .fa-upload{
      font-size:60px;
    }
    .image-upload-container p{
      font-size:20px;
      margin-top:20px;
    }
    .image-upload-container img{
      width:100%;
      height:100%;
      object-fit:cover;
      position:absolute;
      top:0;
      left:0;
      z-index:1;
    }
    
    .image-upload-container input{
      position:absolute;
      top:0;
      left:0;
      width:100%;
      height:100%;
      opacity:0;
      z-index:2;
    }
    .image-upload-container .icon-container{
      font-size:50px;
      z-index:3;
      position:absolute;
      top:50%;
      left:50%;
      transform:translateX(-50%) translateY(-50%);
      transition:all .2s;
      -webkit-transition:all .2s;
      .fa{
        color:$light-text-color;
        margin-left:20px;
        margin-right:20px;
      }
    }
    .image-upload-container .fa:hover{
      color:$form-success;
    }

    .length-counter{
      position:absolute;
      bottom:7px;
      right:10px;
      padding:10px;
      color:rgba(0,0,0,.2);
      font-size:12px;
      font-weight:900;
      border-radius:0 0 0 10px;
      &.warn{
        color:$form-warn;
      }
      &.error{
        color:$form-error;
      }
    }


</style>
