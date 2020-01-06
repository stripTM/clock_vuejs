<template>
  <div id="app" ref="app">
    <transition name="move">
      <clockDesktop :time="now" :style="getStyle" v-show="!position.moving" @resize="changeClockSize"/>
    </transition>
  </div>
</template>

<script>
import clockDesktop from './components/clockDesktop.vue'

export default {
  name: 'app',
  components: {
    clockDesktop
  },
  data() {
    return {
      intervalID: null,
      now: new Date(),
      // position [0..100]
      position: {
        top: 50,
        left: 50,
        moving: false
      },
      appSize: {
        width: 0,
        height: 0
      },
      clockSize: {
        width: 0,
        height: 0
      },
      log: {}
    }
  },
  created() {
    this.now = new Date()
    // Sincronizar los interval con el segundo 0
    //const millisecsToZero = 1000 * (60 - new Date().getSeconds())
     const millisecsToZero = 100
    window.setTimeout(
      () => {
        this.now = new Date()
        this.intervalID = window.setInterval(
          () => { this.now = new Date() },
           1000 * 1 //60
        )},
      millisecsToZero
    )
    // Movimiento aleatorio del reloj dentro de la pantall
    window.setInterval(
      () => {
        this.position = {
          moving: true
        }
        window.setTimeout(
          () => {
            this.position = {
              moving: false,
              top: Math.round( Math.random() * 100 ),
              left: Math.round( Math.random() * 100 )
            }
          },
          800
        )
      },
      100003
    )
  },
  mounted() {
    window.onresize = this.setAppSize
    this.setAppSize()
  },
  destroyed() {
    window.clearInterval(this.intervalID)
    this.intervalID = null
  },
  methods: {
    changeClockSize( dimension ) {
      /* eslint-disable */
      //console.log ( 'clock out', dimension )
      /* eslint-enable */
      this.clockSize = dimension
    },
    setAppSize() {
      this.appSize = {
        width: this.$refs.app.clientWidth,
        height: this.$refs.app.clientHeight
      }
    }
  },
  computed: {
    getStyle() {
      return {
        top: (this.appSize.height - this.clockSize.height) * this.position.top / 100 + 'px',
        left: (this.appSize.width - this.clockSize.width) * this.position.left / 100 + 'px'
      }
    }
  }
}
</script>

<style>
body {
  padding: 0;
  margin: 0;
}
#app {
  position: relative;
  box-sizing: content-box;
  overflow: hidden;
  width: 100vw;
  height: 100vh;
  color: #eee;
  background-color: #000;
  font-family: 'Montserrat', sans-serif;
}
.clock {
  position: relative;
  display: inline-block;
  text-align: center;
}
.hour {
  font-size: 27vw;
  line-height: 25vw;
}
.day {
  font-size: 6vw;
}
.move-enter-active {
  transition: opacity .8s ease, transform .8s ease;
}
.move-leave-active {
  transition: opacity .8s ease, transform .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.move-enter, .move-leave-to {
  transform: scale(0.8);
  opacity: 0;
}

@media (orientation: landscape) {
  .hour {
    font-size: 30vw;
    line-height: 28vw;
  }
}
</style>
