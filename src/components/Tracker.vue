<template>
  <span v-show="show" class="flip-clock__piece">
    <span class="flip-clock__card flip-card">
      <b class="flip-card__top">{{zerofill(current)}}</b>
      <b class="flip-card__bottom" :current-value=zerofill(current)></b>
      <b class="flip-card__back" :current-value=zerofill(previous)></b>
      <b class="flip-card__back-bottom" :current-value=zerofill(previous)></b>
    </span>
  </span>
</template>

<script>
export default {
  name: 'tracker',
  props: ['property','time'],
  data: () => ({
    current: 0,
    previous: 0,
    show: false
  }),

  methods: {
    zerofill(value) {
      return ( value < 10 && value > -1 ? '0' : '' ) + value;
    }
  },
  
  created() {
    this.$parent.$on('time', newValue => {
      if ( newValue[this.property] === undefined ) { 
        this.show = false; 
        return;
      }
      
      var val = newValue[this.property];
      this.show = true;
      
      val = ( val < 0 ? 0 : val );
      
      if ( val !== this.current ) {
  
        this.previous = this.current;
        this.current = val;
  
        this.$el.classList.remove('flip');
        void this.$el.offsetWidth;
        this.$el.classList.add('flip');
      }
    });
  }
}
</script>

<style lang="less" scoped>
.flip-clock {
  perspective: 600px;
  margin: 0 auto;

  *,
  *:before,
  *:after { box-sizing: border-box; }
}

.flip-clock__piece {
  display: inline-block;
  text-align: center;
  margin: 0 0.2vw;
  
  @media (min-width: 1000px) {
    margin: 0 5px;
  }
}

@halfHeight: 0.72em;
@borderRadius: 0.15em;

.flip-card {
  display: block;
  position: relative;
  padding-bottom: @halfHeight;
  font-size: 2.25rem;
  line-height: 0.95;
}

.flip-card__top,
.flip-card__bottom,
.flip-card__back-bottom,
.flip-card__back::before,
.flip-card__back::after {
  display: block;
  height: @halfHeight;
  color: rgb(223, 108, 31);
  background: #0e0f7c;
  padding: 0.23em 0.25em 0.4em;
  border-radius: @borderRadius @borderRadius 0 0;
  transform-style: preserve-3d;
  width: 1.8em;
}

.flip-card__bottom,
.flip-card__back-bottom {
  color: rgb(223, 108, 31);
  position: absolute;
  top: 50%;
  left: 0;
  border-top: solid 1px #000;
  background: #0e0f7c;
  border-radius: 0 0 @borderRadius @borderRadius;
  pointer-events: none;
  overflow: hidden;
  z-index: 2;
}

.flip-card__back-bottom {
  z-index: 1;
}

.flip-card__bottom::after,
.flip-card__back-bottom::after {
  display: block;
  margin-top: -@halfHeight;
}

.flip-card__back::before,
.flip-card__bottom::after,
.flip-card__back-bottom::after {
  content: attr(current-value);
}

.flip-card__back {
  position: absolute;
  top: 0;
  height: 100%;
  left: 0%;
  pointer-events: none;
}

.flip-card__back::before {
  position: relative;
  overflow: hidden;
  z-index: -1;
}

.flip .flip-card__back::before {
  z-index: 1;
  animation: flipTop 0.3s cubic-bezier(.37,.01,.94,.35);
  animation-fill-mode: both;
  transform-origin: center bottom;
}

.flip .flip-card__bottom {
  transform-origin: center top;
  animation-fill-mode: both;
  animation: flipBottom 0.6s cubic-bezier(.15,.45,.28,1);
}

@keyframes flipTop {
  0% {
    transform: rotateX(0deg);
    z-index: 2;
  }
  0%, 99% {
    opacity: 1;
  }
  100% {
    transform: rotateX(-90deg);
    opacity: 0;
  }
}

@keyframes flipBottom {
  0%, 50% {
    z-index: -1;
    transform: rotateX(90deg);
    opacity: 0;
  }
  51% {
    opacity: 1;
  }
  100% {
    opacity: 1;
    transform: rotateX(0deg);
    z-index: 5;
  }
}

</style>