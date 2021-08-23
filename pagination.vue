<template>
  <section class="ezpz-pagination" v-if="parseInt(totalPages) > 1">
      <div class="back-arrow pager" v-on:click="updateActiveOrderPage(value - 1)">
        <span class="fa fa-chevron-left"></span>
      </div>
      <div class="pager" v-on:click="updateActiveOrderPage(1)" v-bind:class="{active: 1 == value}">
        1
      </div>
      <div class="ellipsis" v-if="1 - value < -3">...</div>
      <div class="pager" v-on:click="updateActiveOrderPage(page)" v-for="page in parseInt(totalPages)" v-bind:class="{active: page == value}" v-if="page - value < 3 && page - value > -3 && page != totalPages && page != 1">
        {{page}}
      </div>
      <div class="ellipsis" v-if="totalPages - value > 3">...</div>
      <div class="pager" v-on:click="updateActiveOrderPage(totalPages)" v-bind:class="{active: totalPages == value}">
        {{totalPages}}
      </div>
      <div class="forward-arrow pager" v-on:click="updateActiveOrderPage(value + 1)">
        <span class="fa fa-chevron-right"></span>
      </div>

  </section>
</template>
<script>
export default {
  name: 'Pagination',
  components: {

  },
  props: ['value', 'totalPages'],
  mixins: [],
  data: function(){
      return {

      }
  },
  computed: {

  },
  methods: {
    updateActiveOrderPage: function(page){
      this.$emit('input', page);
    }
  }
}
</script>
<style scoped lang="scss">
.ezpz-pagination{
  text-align:center;
  padding-top:40px;
  padding-bottom:10px;
}
.pager{
  display:inline-block;
  width:40px;
  height:40px;
  text-align:center;
  line-height:40px;
  padding:0;
  margin:0;
  font-size:20px;
  background:$dark-bg-color;
  color:$light-text-color;
  border-radius:50%;
  margin-left:2px;
  margin-right:2px;
  vertical-align:middle;
}
.pager.active{
  background:$light-bg-color;
  color:$dark-text-color;
}
.ellipsis{
  display:inline-block;
  vertical-align:middle;
}
.pager.first{
  margin-right:10px;
}
.pager.last{
  margin-left:10px;
}
</style>
