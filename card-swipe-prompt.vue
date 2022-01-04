<template>
    <section class="ezpz-card-swipe-prompt">
        <transition name="zoom">
            <div class="prompt" ref="promptWindow">
                <card-swipe-input class="padded" v-model="cardData"></card-swipe-input>
            </div>
        </transition>
        
        <div class="prompt-background" v-on:click="dismiss"></div>

    </section>
</template>
<script>
import Vuex from 'vuex';

export default {
    components: {
        Vuex
    },
	props: [],
    mixins: [],
    data: function(){
        return {
            
        }
    },
	computed: {
        ...Vuex.mapState(['carts']),
        
	},
	methods: {
        dismiss: function(){
            this.$emit('dismiss');
        },
    },
    mounted: function(){
        this.$root.$el.append(this.$el);
    },
}
</script>
<style scoped lang="scss">
    .prompt-background{
        background:rgba(255,255,255,.5);
        width:100%;
        height:100%;
        position:fixed;
        top:0;
        left:0;
        z-index:1000000000;
    }
    .prompt{
        position:fixed;
        top:50%;
        left:50%;
        transform:translateX(-50%) translateY(-50%);
        z-index:10000000000;
        border-radius:30px;
        width:500px;
        max-height:90vh;
        background:$dark-bg-color;
        color:$light-text-color;
        white-space:normal !important;
        text-align:center;
    }
    .padded{
        padding:15px 30px;
    }
        
</style>
