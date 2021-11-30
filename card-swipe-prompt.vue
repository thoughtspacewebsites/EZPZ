<template>
    <section class="ezpz-card-swipe-prompt">
        <transition name="zoom">
            <div class="prompt" ref="promptWindow">
                <div class="swipe-card padded" v-if="!extractedLineOne">
                    <notification type="info" v-if="!swipeError">
                        Please swipe a card
                    </notification>
                    <notification type="error" v-if="swipeError">
                        There was an issue reading this card, please swipe again.
                    </notification>
                    <loading-spinner type="dark" size="small"></loading-spinner>
                    <h2>Waiting for card swipe...</h2>
                    <button class="light full-width" v-on:click="dismiss">Cancel</button>
                </div>
                <loading-spinner v-if="loading" type="dark"></loading-spinner>

                <input type="text" ref="cardNumber" class="card-number no-keyboard" v-model="cardNumber" />


            </div>
        </transition>
        
        <div class="prompt-background" v-on:click="dismiss"></div>

    </section>
</template>
<script>
import Vuex from 'vuex';
import Notification from 'ezpz/notification.vue';
import LoadingSpinner from 'ezpz/loading-spinner.vue';

export default {
    components: {
        Vuex, Notification, LoadingSpinner
    },
	props: [],
    mixins: [],
    data: function(){
        return {
            cardNumber: "",
            extractedLineOne: false,
            swipeError: false,
            loading: false,
            updatingCard: false
        }
    },
	computed: {
        ...Vuex.mapState(['carts']),
        
	},
	methods: {
        reload: function(){
            this.$emit('reload');
        },
        dismiss: function(){
            this.$emit('dismiss');
        },
    },
    mounted: function(){
        //Focus the card number input on mount, and refocus on click off if we don't have a number yet
        this.$root.$el.append(this.$el);
        
        if(this.$refs.cardNumber){
            this.$refs.cardNumber.focus();
            var that = this;
            this.$refs.promptWindow.addEventListener('click', function(){
                if(!that.cardNumber && that.$refs && that.$refs.cardNumber){
                    that.$refs.cardNumber.focus();
                }
            });
        }
        
    
    },
    watch: {
        cardNumber: function(val){
            if(val){
                var that = this;
                clearTimeout(this.updatingCard);
                this.updatingCard = setTimeout(function(){
                    var hasError = false
                    var cardLines = val.substring(1, val.length -1).split(/(?:\?\;|\?\+|\?\%)+/);
                
                    var lineOne = cardLines[0];

                    //Check for error
                    for(var l in cardLines){
                        if(cardLines[l] == 'E'){
                            that.cardNumber = "";
                            hasError = true;
                        }
                    }

                    //Return line two if no error

                    if(!hasError){
                        that.extractedLineOne = lineOne;
                        that.swipeError = false;
                        that.$refs.cardNumber.blur();

                        //Now send the update event and the dismiss event
                        that.$emit('input', lineOne);
                        that.$emit('dismiss');
                    }
                    else{
                        that.swipeError = true;
                        that.extractedLineOne = false;
                        that.$refs.cardNumber.focus();
                    }
                    
                }, 100);
                
            }
        }
    }
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
    .swipe-card h2{
        font-size:20px;
    }
    .prompt-content{
        text-align:left;
        height:100%;
        max-height:90vh;
        overflow:scroll;
        border-radius:30px;
    }
    .padded{
        padding:15px 30px;
    }
    .prompt::v-deep .ezpz-not-content{
        white-space:normal !important;
    }
    .card-number{
        opacity:0;
        height:0;
        overflow:hidden;
    }
    
</style>
