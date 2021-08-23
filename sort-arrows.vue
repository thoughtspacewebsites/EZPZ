<template>
  <section class="ezpz-sort-arrows" v-bind:class="direction">
    <div class='sort-asc fa fa-sort-up'></div>
    <div class='sort-desc fa fa-sort-down'></div>
  </section>
</template>
<script>
export default {
  name: 'SortArrows',
  components: {

	},
	props: ['direction'],
  mixins: [],
	data: function(){
	    return {

	    }
	},
	computed: {

  },
  methods: {

  }
}
</script>
<style scoped lang="scss">
.ezpz-sort-arrows{
  position:relative;
  display:inline-block;
  vertical-align:middle;
  height:20px;
}
.fa-sort-up,
.fa-sort-down{
  position:absolute;
  top:0;
  left:0;
  color:$dark-text-color;
}
.asc .fa-sort-down{
  opacity:.2;
}
.desc .fa-sort-up{
  opacity:.2;
}
</style>
