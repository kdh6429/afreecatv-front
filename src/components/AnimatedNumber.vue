<template>
<div class="count" :class="effectCss">{{displayNumber}}</div>
</template>
<style>
.count {
    display: inline-block;
}
.downeffect {
    animation: testdowneffect 0.5s;
    color: blue;
}
.upeffect {
    animation: testupeffect 0.5s;
    color: red;
}
@keyframes testdowneffect {
    0% {
        transform: scale(1,1);
    }
    50% {
        transform: scale(0.9,0.9);
    }
    100% {
        transform: scale(1,1);
    }
}
@keyframes testupeffect {
    0% {
        transform: scale(1,1);
    }
    50% {
        transform: scale(1.1,1.1);
    }
    100% {
        transform: scale(1,1);
    }
}
</style>
<script>
export default {
  name: "animatednumber",
  props: {
      'number': { default:0 },
      'prevNumber': { default:0 }
    },

  data () {
    return {
      displayNumber:0,
      interval:false,
      change: 0,
      effectCss: '',
    }
  },
  created: function () {
    this.displayNumber = this.prevNumber ? this.prevNumber : 0;
  },
  watch: {
    number () {
      this.setCountTimer();
    },
    prevNumber() {
      this.setCountTimer();
    }
  },
  methods: {
      setCountTimer() {

        clearInterval(this.interval);
        if (this.displayNumber > this.prevNumber) {
            this.effectCss = 'downeffect';
        }
        else if (this.displayNumber < this.prevNumber) {
            this.effectCss = 'upeffect';
        }
        window.setTimeout(() => {
            this.effectCss = '';
        }, 500);

        this.displayNumber = this.prevNumber;
        if(this.number == this.displayNumber) {
            return;
        }

        //800 ~ 1200
        const random_interval = Math.floor(Math.random() * 400) + 900;
        const random_divider = Math.floor(Math.random() * 2) + 6;

        this.interval = window.setInterval(() => {
            if(this.displayNumber == this.number) {
                return;
            }
            var change = (this.number - this.prevNumber) / random_divider;
            change = change >= 0 ? Math.ceil(change) : Math.floor(change);
            if( (change>0 && this.displayNumber + change > this.number) || 
                (change<0 && this.displayNumber + change < this.number)) {
                this.setEffect( change) 
                this.displayNumber = this.number;
                change = 0;
            }
            else if(this.displayNumber != this.number) {
                this.displayNumber = this.displayNumber + change;
            
                this.change = change;
                this.setEffect( change) 
            }
        }, random_interval);
      },
      setEffect( change) {
        if( change == 0) return 
        if (change<0) {
            this.effectCss = 'downeffect';
        }
        else if (change > 0) {
            this.effectCss = 'upeffect';
        }
        
        window.setTimeout(() => {
            this.effectCss = '';
        }, 500);
      }
  }
}
</script>