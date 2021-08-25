<template>
  <section v-bind:id="'ezpz-modal-container-'+id" class="ezpz-modal-container" v-bind:class="{'dark-mode': darkMode}">
    <div class="ezpz-background" v-on:click="closeModal()"></div>
    <div class="ezpz-modal" v-bind:class="{'thin': thin, 'full-screen': fullScreen, 'error': error}">
      <span class="fa fa-times" v-if="!closeDisabled" v-on:click="closeModal()"></span>
      <div class="ezpz-modal-content">
        <slot></slot>
      </div>
    </div>
  </section>
</template>
<script>

export default {
  name: 'Modal',
  components: {

	},
	props: ['thin', 'closeDisabled', 'fullScreen', 'darkMode', 'error'],
  mixins: [],
	data: function(){
	    return {
        id: null
	    }
	},
	computed: {

  },
  methods: {
    closeModal: function(){
      if(!this.closeDisabled){
        this.$emit('close');
      }

    }
  },
  mounted: function(){
    this.id = this._uid;
    this.$root.$el.append(this.$el);
  }
}
</script>
<style scoped lang="scss">
.ezpz-modal-container{
  position:fixed;
  top:0;
  left:0;
  width:100%;
  height:100vh;
  z-index:400000;
}
.ezpz-background{
  width:100%;
  height:100%;
  background:rgba(0,0,0,.75);
}
.ezpz-modal{
  position:absolute;
  top:50%;
  left:50%;
  background:$light-bg-color;
  color:$dark-text-color;
  width:700px;
  max-height:80vh;
  border-radius:10px;
  transform:translateX(-50%) translateY(-50%);
  padding:30px;
}
.dark-mode .ezpz-modal{
  background:$dark-bg-color;
  color:$light-text-color;
}
.ezpz-modal-content{
  width:100%;
  max-height:calc(80vh - 60px);
  overflow:scroll;
}
.ezpz-modal.thin{
  width:500px;
}
.ezpz-modal.full-screen{
  width:95vw;
  height:95vh;
  max-height:95vh;
  overflow:hidden;
}
.ezpz-modal.error{
  background:$form-error;
  color:$light-text-color;
}
.fa-times{
  position:absolute;
  top:-20px;
  right:-20px;
  color:$light-text-color;
  background:$form-error;
  width:50px;
  height:50px;
  text-align:center;
  line-height:50px;
  font-size:30px;
  border-radius:50%;
}
.ezpz-modal.full-screen .fa-times{
  top:-2px;
  right:-2px;
  border-radius:0;
  border-bottom-left-radius:10px;
}
</style>
