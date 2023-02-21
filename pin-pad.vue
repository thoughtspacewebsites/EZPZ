<template>
  <section class="pin-pad-container" v-bind:class="[size, {'dark-mode': darkMode}]">
    <div class="input-container">
      <form-field type="number" v-model="typedNumber" field="typedNumber" label="<span class='fa fa-chevron-right'></span>" focus-on-create="true"></form-field>
    </div>
    <div class="pin-pad" >
      <div class="number flash-effect" v-for="number in (1, 9)" v-on:click="updateValue(number)">{{number}}</div>
      <div class="number" v-if="!canBeNegative || !value"></div>
      <div class="number flash-effect" v-if="canBeNegative && value" v-on:click="updateValue('-')">-</div>
      <div class="number flash-effect" v-on:click="updateValue(0)">0</div>
      <div class="number flash-effect" v-on:click="backspaceValue()">
          <span class="fa fa-backspace"></span>
      </div>
    </div>
  </section>
</template>
<script>
import FormField from './form-field.vue';
export default {
  name: 'PinPad',
  components: {
    FormField
	},
	props: ['value', 'darkMode', 'canBeNegative', 'size'],
	data: function(){
	    return {

	    }
	},
  mixins: [],
	computed: {
    typedNumber: {
      get: function(){
        return this.value/100;
      },
      set: function(val){
        this.$emit('input', val*100);
      }
    }
	},
	methods: {
    updateValue: function(number){
      var newVal = "";
      if(this.value){
        if(number == "-"){
          number = parseInt(number);
          if(this.value){
            newVal = -this.value;
          }
          
          newVal = newVal.toString();
        }
        else{
          newVal = this.value.toString()+number;
        }
      }
      else if(number != "-"){
        newVal = number.toString();
      }
      this.$emit('input', parseInt(newVal));
    },
    backspaceValue: function(){
      if(this.value){
        if(this.value.toString().length > 1){
          this.$emit('input', parseInt(this.value.toString().slice(0, -1)));
        }
        else{
          this.$emit('input', false);
        }
      }
    }
	},
  created: function(){

  }
}
</script>
<style scoped lang="scss">
.input-container{
  position:relative;
}
.input-container::v-deep .ezpz-field-wrap{
  margin-bottom:0;
}
.input-container::v-deep input{
border:none;
background:rgba(0,0,0,.1);
border-radius:0;
padding:10px 30px 10px 50px;
}

.input-container::v-deep label{
  position:absolute;
  top:50%;
  left:30px;
  transform:translate(0, -50%);
  padding:0;
  color:$dark-text-color;
  font-size:14px;
  font-weight:400;
  pointer-events:none;
  margin:0;
}
.dark-mode::v-deep label{
  color:$light-text-color;
}
.input-container::v-deep input::-webkit-outer-spin-button,
.input-container::v-deep input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

.dark-mode::v-deep input{
  background:rgba(255,255,255,.2);
  color:$light-text-color;
}
  
.pin-pad {
display: flex;
flex-wrap: wrap;
justify-content: space-between;
width:100%;
height:auto;
}
.number {
width: 32%;
height:100px;
font-size:35px;
font-weight:400;
color:$dark-text-color;
display:flex;
align-items:center;
vertical-align: center;
justify-content: center;
flex-grow:1;
border-bottom:1px solid rgba(0,0,0,.1);
border-right:1px solid rgba(0,0,0,.1);
}
.dark-mode .number{
  color:$light-text-color;
  border-bottom:1px solid rgba(255,255,255,.1);
  border-right:1px solid rgba(255,255,255,.1);
}
.number:nth-child(1),
.number:nth-child(2),
.number:nth-child(3){
  border-top:1px solid rgba(0,0,0,.1);
}
.number:nth-child(1),
.number:nth-child(4),
.number:nth-child(7),
.number:nth-child(10){
  border-left:1px solid rgba(0,0,0,.1);
}
.dark-mode .number:nth-child(1),
.dark-mode .number:nth-child(2),
.dark-mode .number:nth-child(3){
  border-top:1px solid rgba(255,255,255,.1);
}
.dark-mode .number:nth-child(1),
.dark-mode .number:nth-child(4),
.dark-mode .number:nth-child(7),
.dark-mode .number:nth-child(10){
  border-left:1px solid rgba(255,255,255,.1);
}


.pin-pad-container.small .number{
  height:70px;
}
</style>
