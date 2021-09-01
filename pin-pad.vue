<template>
  <div class="pin-pad" v-bind:class="{'dark-mode': darkMode}">
    <div class="number flash-effect" v-for="number in (1, 9)" v-on:click="updateValue(number)">{{number}}</div>
    <div class="number"></div>
    <div class="number flash-effect" v-on:click="updateValue(0)">0</div>
    <div class="number flash-effect" v-on:click="backspaceValue()">
        <span class="fa fa-backspace"></span>
    </div>
  </div>
</template>
<script>
export default {
  name: 'PinPad',
  components: {

	},
	props: ['value', 'darkMode'],
	data: function(){
	    return {

	    }
	},
  mixins: [],
	computed: {

	},
	methods: {
    updateValue: function(number){
      var newVal = "";
      if(this.value){
        newVal = this.value.toString()+number;
      }
      else{
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
</style>
