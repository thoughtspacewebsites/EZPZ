<template>
  <section class="ezpz-timer" v-bind:class="{running: timer}">

    <div class="controls">
      <transition name="zoom" mode="out-in">
        <div class="start-timer" v-on:click="startTimer()" v-if="!timer" key="start">Clock In</div>
        <div class="stop-timer" v-on:click="stopTimer()" v-if="timer" key="stop">Clock Out</div>
      </transition>
    </div>

    <div class="background-icon right fa fa-clock"></div>
    <div class="timer">
      <div class="digits hours">{{hours}}</div>
      <div class="digits minutes">{{minutes}}</div>
      <div class="digits seconds">{{seconds}}</div>
    </div>

    <alert type="warn" has-confirm="true" v-bind:message="stopConfirmation" v-on:confirm="confirmStopTimer" v-on:dismiss="stopConfirmation = false"></alert>
  </section>
</template>
<script>
import Alert from './alert.vue';
export default {
  name: 'Timer',
  components: {
    Alert
	},
	props: ['resetTrigger', 'startTrigger', 'stopTrigger'],
  mixins: [],
	data: function(){
	    return {
        hours: "00",
        minutes: "00",
        seconds: "00",
        timer: false,
        startTime: false,
        stopConfirmation: false
	    }
	},
	computed: {


  },
  methods: {
    startTimer: function(forceStartTime){
      //Start locally incrementing the timer
      if(!this.startTime){
        if(forceStartTime && forceStartTime != ""){
          this.startTime = new Date(forceStartTime);
        }
        else{
          this.startTime = new Date();
        }
      }
      this.timer = this.incrementTimer();
      this.$emit('timer-started', this.startTime.toUTCString());
    },
    stopTimer: function(){
      this.stopConfirmation = "Are you sure you want to clock out?";
    },
    confirmStopTimer: function(){
      if(this.timer){
        clearInterval(this.timer);
        this.timer = false;
        this.stopConfirmation = false;
        this.$emit('timer-stopped', new Date().toUTCString());
      }
    },
    resetTimer: function(){
      this.startTime = false;
      this.hours = "00";
      this.minutes = "00";
      this.seconds = "00";
    },
    incrementTimer: function(){
      var that = this;
      return setInterval(function(){
        var curTime = new Date();
        var elapsedSeconds = Math.floor((curTime - that.startTime) / 1000);
        that.hours = Math.floor(elapsedSeconds/60/60).toString().padStart(2, '0');
        that.minutes = (Math.floor(elapsedSeconds/60) - (that.hours*60)).toString().padStart(2, '0');
        that.seconds = (elapsedSeconds%60).toString().padStart(2, '0');
      }, 1000)
    }
  },

  watch: {
    resetTrigger: function(val){
      if(val){
        this.resetTimer();
      }
    },
    startTrigger: function(val){
      if(val){
        this.startTimer(val);
      }
    },
    stopTrigger: function(val){
      if(val){
        this.confirmStopTimer();
      }
    }
  }
}
</script>
<style scoped lang="scss">
.ezpz-timer{
  position:relative;
  z-index:1;
  overflow:hidden;
  text-align:center;
  display:flex;
  align-items:center;
  padding:0px 25px;
  padding-right:125px;
  background:rgba(0,0,0,.2);
  border-radius:100px;
  transition:all .2s;
}
.timer{
  font-size:80px;
  display:flex;
  align-items:center;
  justify-content:center;
  font-weight:700;
  color:$light-text-color;
  margin-left:auto;
}
.timer .digits:not(:first-of-type):before{
  content:":";
  font-size:60px;
}
.controls{
display:inline-block;
vertical-align:center;
text-align:center;
font-size:100px;
margin-top:20px;
margin-bottom:20px;
}

.controls .start-timer,
.controls .stop-timer{
  background:$dark-bg-color;
  color:$light-text-color !important;
  border-radius:500px;
  padding:0px 50px;
}
.controls .stop-timer{
  color:$form-error;
  background:$form-error !important;
}
</style>
