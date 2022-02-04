<template>
    <section class="ezpz-card-swipe-input" ref="inputWindow">
        <div class="swipe-card" v-if="!extractedLineOne">
            <notification type="info" v-if="!swipeError">
                Please swipe a card
            </notification>
            <notification type="error" v-if="swipeError">
                There was an issue reading this card, please swipe again.
            </notification>
            <loading-spinner type="dark" size="small"></loading-spinner>
            <h2>Waiting for card swipe...</h2>
        </div>
        <loading-spinner v-if="loading" type="dark"></loading-spinner>

        <input type="text" ref="cardNumber" class="card-number no-keyboard" v-model="cardNumber" />
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
            extractedLineTwo: false,
            swipeError: false,
            loading: false,
            updatingCard: false
        }
    },
	computed: {
        ...Vuex.mapState(['carts']),
        
	},
	methods: {

    },
    mounted: function(){
        //Focus the card number input on mount, and refocus on click off if we don't have a number yet
        
        if(this.$refs.cardNumber){
            this.$refs.cardNumber.focus();
            var that = this;
            this.$refs.inputWindow.addEventListener('click', function(){
                if(!that.cardNumber && that.$refs && that.$refs.cardNumber){
                    that.$refs.cardNumber.focus();
                }
            });
            this.$nextTick(function(){
                that.$refs.cardNumber.focus();
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
                    var lineTwo = cardLines[1];

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
                        that.extractedLineTwo = lineTwo;
                        that.swipeError = false;
                        that.$refs.cardNumber.blur();

                        //Now send the update event and the dismiss event
                        that.$emit('input', {
                            lineOne: lineOne,
                            lineTwo: lineTwo
                        });
                    }
                    else{
                        that.swipeError = true;
                        that.extractedLineOne = false;
                        that.extractedLineTwo = false;
                        that.$refs.cardNumber.focus();
                    }
                    
                }, 100);
                
            }
        }
    }
}
</script>
<style scoped lang="scss">
    .swipe-card h2{
        font-size:20px;
    }
    .input::v-deep .ezpz-not-content{
        white-space:normal !important;
    }
    .card-number{
        opacity:0;
        height:0;
        overflow:hidden;
    }
    
</style>
