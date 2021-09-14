<template>
    <section class="ezpz-universal-keyboard" v-bind:class="{'pin-pad': isPinPad}">
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
    props: ['enableKeyboard', 'enablePinPad'],
    data: function(){
        return {
            keyboard: null,
            keyboardClass: 'universal-keyboard',
            activeInput: false,
            willDestroy: false,
            initing: false,
            isPinPad: false,
            pinPadLayout: {
                default: ["1 2 3", "4 5 6", "7 8 9", " 0 ", "{bksp}"]
            },
            padTheme: "hg-theme-default hg-layout-numeric numeric-theme"
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
                document.querySelector('.uk-active-input').classList.remove('uk-active-input');
                if(!withAnimate){
                    this.keyboard.destroy();
                    this.keyboard = false;
                    this.activeInput = false;
                    this.isPinPad = false;
                }
                else{
                    var that = this;
                    that.activeInput = false;
                    setTimeout(function(){
                        that.keyboard.destroy();
                        that.keyboard = false;
                        that.isPinPad = false;
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
            if(
                event.target.localName == "input" && 
                (
                    event.target.type == 'number' && this.enablePinPad ||
                    event.target.type != 'number' && this.enableKeyboard
                ) &&
                !event.target.classList.contains('no-keyboard') &&
                (!this.keyboard || this.activeInput != event.target.id)
            ){
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
                event.target.classList.add('uk-active-input');
     
                this.$nextTick(function(){
                    
                    //Now make a new keyboard for this input...
                    var kbOptions = {
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
                    };
                    if(event.target.type == "number"){
                        that.isPinPad = true;
                        kbOptions.layout = that.pinPadLayout;
                        kbOptions.theme = that.padTheme;
                    }
                    that.keyboard = new Keyboard(that.keyboardClass, kbOptions);
                    if(event.target.value != ""){
                        that.keyboard.setInput(event.target.value);
                    }
                    that.keyboardVisible = true;

                    document.querySelector("#"+that.activeInput).addEventListener('keyup', that.watchPhysicalKeyboard);
                    document.querySelector('.universal-keyboard').addEventListener('click', that.stopDestroy)
                    document.body.addEventListener('click', that.hideOnClick);
                    
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
    },
}
</script>
<style lang="scss">
    .ezpz-universal-keyboard{
        width:100%;
        height:auto;
        position:fixed;
        bottom:0;
        left:0;
        z-index:400000000000;
    }
    .ezpz-universal-keyboard.pin-pad{
        width:400px;
        bottom:20px;
        left:20px; 
    }
    .ezpz-universal-keyboard .universal-keyboard{
        background:#333333;
        position:relative;
        z-index:4000000000000000;
    }
    .ezpz-universal-keyboard.pin-pad .universal-keyboard{
        border-radius:30px;
    }
    .ezpz-universal-keyboard.pin-pad .universal-keyboard .hg-row:first-of-type .hg-button:first-of-type{
        border-top-left-radius:30px;
    }
    .ezpz-universal-keyboard.pin-pad .universal-keyboard .hg-row:first-of-type .hg-button:last-of-type{
        border-top-right-radius:30px;
    }
    .ezpz-universal-keyboard.pin-pad .universal-keyboard .hg-row:last-of-type .hg-button{
        border-bottom-left-radius:30px;
        border-bottom-right-radius:30px;
    }
    .hg-theme-default .hg-button{
        height:50px;
        font-size:18px;
        background:$dark-bg-color;
        color:$light-text-color;
        border-bottom:none;
    }
    .hg-theme-default .hg-activeButton{
        background:$form-success !important;
    }
</style>