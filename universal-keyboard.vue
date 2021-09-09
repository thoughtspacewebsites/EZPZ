<template>
    <section class="ezpz-universal-keyboard">
        <transition v-bind:name="animationName">
            <div v-if="activeInput">
                <div v-bind:class="keyboardClass" v-if="activeInput"></div>
            </div>
        </transition>
        
    </section>
</template>
<script>
import Keyboard from 'simple-keyboard';
import 'simple-keyboard/build/css/index.css';

export default{
    name: 'UniversalKeyboard',
    components: {},
    props: [],
    data: function(){
        return {
            keyboard: null,
            keyboardClass: 'universal-keyboard',
            activeInput: false,
            willDestroy: false,
            initing: false,
        }
    },
    computed: {
        animationName: function(){
            if(this.keyboard){
                return 'slideDown';
            }
            else{
                return 'slideUp';
            }
        }
    },
    methods: {
        tearDownKeyboard: function(withAnimate){
            
            if(this.activeInput){
                //Remove our event listeners...
                document.querySelector("#"+this.activeInput).removeEventListener('keyup', this.watchPhysicalKeyboard);
                document.querySelector('.universal-keyboard').removeEventListener('click', this.stopDestroy)
                document.body.removeEventListener('click', this.hideOnClick);
                if(this.activeInput == 'uk-active-input'){
                    document.querySelector('#uk-active-input').id = "";
                }
                if(!withAnimate){
                    this.keyboard.destroy();
                    this.keyboard = false;
                    this.activeInput = false;
                }
                else{
                    var that = this;
                    that.activeInput = false;
                    setTimeout(function(){
                        that.keyboard.destroy();
                        that.keyboard = false;
                    }, 500)
                }
                
            }
            

        },
        hideOnClick: function(event){
            if(this.initing){
                this.initing = false;
                return;
            }
            if(!event.target.className.includes('hg-button') && event.target.id != this.activeInput && this.keyboard){
                var that = this;
                //If we clicked a new input, animate shit
                if(this.activeInput){
                    if(event.target.localName == "input"){
                        this.$nextTick(function(){
                            that.tearDownKeyboard();   
                        })
                    }
                    else{
                        that.tearDownKeyboard(true);
                    }
                }
                
                
            }
        },
        stopDestroy: function(event){
            if(event.target.className.includes('hg-button')){
                clearTimeout(this.willDestroy);
            }
            
        },
        watchPhysicalKeyboard: function(event){
            this.keyboard.setInput(event.target.value);
        },
        onInputFocus: function(event){
            if(event.target.localName == "input" && (!this.keyboard || this.activeInput != event.target.id)){
                clearTimeout(this.willDestroy);

                if(this.activeInput && this.activeInput != event.target.id){
                    this.tearDownKeyboard();
                }
                else if(!this.activeInput){
                    this.initing = true;
                }


                var that = this;
                this.activeInput = event.target.id && event.target.id != "" ? event.target.id : 'uk-active-input'
                event.target.id = this.activeInput;
                //We need a watcher to see if the click was on our keyboard, and if so, clear the timeout for 
                //destroying the keyboard
                this.$nextTick(function(){
                    
                    //Now make a new keyboard for this input...
                    that.keyboard = new Keyboard(that.keyboardClass, {
                        onChange: function(input){
                            var active = "#"+that.activeInput;
                            document.querySelector(active).setAttribute("value", input);
                            document.querySelector(active).dispatchEvent(new Event('input'));
                            console.log("Input changed", input);
                        },
                        onKeyPress: function(button){
                            console.log("Button pressed", button);
                            
                            
                        },
                        inputName: that.activeInput
                    });
                    if(event.target.value != ""){
                        that.keyboard.setInput(event.target.value);
                    }
                    that.keyboardVisible = true;

                    document.querySelector("#"+this.activeInput).addEventListener('keyup', that.watchPhysicalKeyboard);
                    document.querySelector('.universal-keyboard').addEventListener('click', that.stopDestroy)
                    document.body.addEventListener('click', this.hideOnClick);
                    
                });
            }
        },
        onInputFocusLost: function(event){

            //Destroy the keyboard
            var that = this;
            this.keyboardVisible = false;
            this.willDestroy = setTimeout(function(){
                that.tearDownKeyboard(true);
            }, 20);
            
        }
    },
    created: function(){
        
        document.body.addEventListener('focusin', this.onInputFocus);
        document.body.addEventListener('focusout', this.onInputFocusLost);
    },
    beforeDestroy: function(){
        document.body.removeEventListener('focusin', this.onInputFocus);
        document.body.removeEventListener('focusout', this.onInputFocusLost);
    }
}
</script>
<style lang="scss">
    .ezpz-universal-keyboard{
        width:100%;
        height:auto;
        position:fixed;
        bottom:0;
        left:0;
        z-index:100000000;
    }
    .hg-theme-default .hg-button{
        height:50px;
        font-size:18px;
    }
</style>