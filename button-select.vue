<template>
  <section class="ezpz-button-select">
    <div class="option" v-for="option in options" v-on:click="selectOption(option)" v-bind:class="{active: isSelected(option), 'has-image': option.image}" v-bind:style="{backgroundImage: 'url('+option.image+')'}">
      <div>
        <span class="label">{{option.value}}</span>
        <span class="price" v-if="option.price">Adds ${{option.price}}</span>
        <transition name="zoom">
          <span class="selected-label fa fa-check" v-if="isSelected(option)"></span>
        </transition>
      </div>
    </div>
  </section>
</template>
<script>
var _ = require('lodash');
export default {
  name: 'ButtonSelect',
  components: {

	},
	props: ['options', 'selected', 'allowMultiple', 'maxSelections', 'value'],
	data: function(){
	    return {
        tempSelected: []
	    }
	},
	computed: {

	},
	methods: {
    selectOption: function(option){

      var returnVal = false;
      if(this.allowMultiple){
        var matchedIndex = false;
        for(var i in this.tempSelected){
          if(_.isEqual(this.tempSelected[i], option)){
            matchedIndex = i;
          }
        }
        if(matchedIndex === false && (!this.maxSelections || this.tempSelected.length < this.maxSelections)){
          this.tempSelected.push(option);
        }
        else if(matchedIndex){
          this.tempSelected.splice(matchedIndex, 1);
        }
        returnVal = this.tempSelected;
      }
      else{
        returnVal = option;
      }
      this.$emit('input', returnVal);
    },
    isSelected: function(option){
      if(this.allowMultiple){
        if(this.tempSelected && this.tempSelected.length){
          for(var i in this.tempSelected){
            if(_.isEqual(this.tempSelected[i].value, option.value)){
              return true;
            }
          }
        }
      }
      else{
        return _.isEqual(this.selected, option);
      }

      return false;

    },

	},
  watch: {
    selected: function(val){
      this.tempSelected = val;
    }
  },
  mounted: function(){
    this.tempSelected = this.selected ? this.selected : [];
    
  }
}
</script>
<style scoped lang="scss">
.ezpz-button-select{
  display: flex;
  flex-wrap:wrap;
  align-items:center;
  justify-content:center;
}
.option{
  position:relative;
  border:2px solid rgba(0,0,0,.1);
  color:rgba(0,0,0,.5);
  cursor:pointer;
  transition:all .2s;
  -webkit-transition:all .2s;
  border-radius:5px;
  margin:10px;
  font-size:16px;
  align-self: stretch;
  width:calc(25% - 20px);
  flex-basis:calc(25% - 20px);
  font-weight:700;
  text-align:center;
  display: flex;
  align-items: center;
  justify-content: center;

}
.option.active{
  background-color:$dark-bg-color;
  color:$light-text-color;
}
.option.has-image{
  border:none !important;
  background-size:cover;
  background-position:center center;
  background-repeat:no-repeat;
  min-height:125px;
}
.option.has-image:before{
  content:"";
  background:rgba(255,255,255,.5);
  position:absolute;
  z-index:2;
  top:0;
  left:0;
  width:100%;
  height:100%;
  border-radius:5px;
  transition:all .2s;
  -webkit-transition:all .2s;
}
.option.active.has-image{
  background-color:transparent;
}
.option.active.has-image:before{
  background-color:rgba(0,0,0,.3);
}
.option>div{
  position:relative;
  z-index:10;
  width:100%;
  height:100%;
  padding:10px 25px;
  display:flex;
  align-items:center;
  justify-content:center;
  flex-wrap:wrap;
}
.option>div span{
  display:block;
  width:100%;
}
.option .price{
  font-size:15px;
  font-weight:300;
  display:block;
  font-style:italic;
  margin-top:0px;
}
.selected-label{
  background:$form-success;
  width:40px !important;
  height:40px;
  text-align:center;
  line-height:40px;
  border-radius:50%;
  position:absolute;
  top:0;
  left:0;
  transform:translateX(-50%) translateY(-50%);
}
</style>
