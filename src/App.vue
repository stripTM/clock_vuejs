<template>
  <div id="app" ref="app" v-on:click="toggleFullscreen">
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
    const millisecsToZero = 1000 * (60 - new Date().getSeconds())
    window.setTimeout(
      () => {
        this.now = new Date()
        // Refresco de la fecha cada minuto
        this.intervalID = window.setInterval(
          this.update,
          1000 * 60 //60
        )
      },
      millisecsToZero
    )

    // Pantalla completa
    document.fullscreenElement = document.fullscreenElement
      || document.mozFullscreenElement
      || document.msFullscreenElement
      || document.webkitFullscreenDocument
    document.exitFullscreen = document.exitFullscreen
      || document.mozExitFullscreen
      || document.msExitFullscreen
      || document.webkitExitFullscreen;
  },
  mounted() {
    // Cambio de orientación de pantalla
    window.addEventListener('resize', this.setAppSize)
    this.setAppSize()
  },
  destroyed() {
    window.clearInterval(this.intervalID)
    this.intervalID = null
  },
  methods: {
    update() {
      const now = new Date()
      // Lanzar animación aleatoriamente
      const withAnimation = Math.random() > 0.8
      if (withAnimation) {
        // Movimiento aleatorio del reloj dentro de la pantalla
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
            this.now = now
          },
          800
        )
      }
      else {
        this.now = now
      }
    },
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
    },
    toggleFullscreen() {
      let elem = this.$refs.app

      elem.requestFullscreen = elem.requestFullscreen || elem.mozRequestFullscreen
              || elem.msRequestFullscreen || elem.webkitRequestFullscreen;

      if (!document.fullscreenElement) {
        elem.requestFullscreen().then({}).catch(err => {
          alert(`Error attempting to enable full-screen mode: ${err.message} (${err.name})`);
        });
      } else {
        if (document.exitFullscreen) {
          document.exitFullscreen();
        }
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
  font-size: 60vw;
  line-height: 58vw;
}
.hour .digit {
  display: block;
}
.day {
  font-size: 10vw;
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
.portrait {
  display: block;
}
.landscape {
  display: none;
}
@media (orientation: landscape) {
  .portrait {
    display: none;
  }
  .landscape {
    display: block;
  }
  .hour {
    font-size: 30vw;
    line-height: 28vw;
  }
  .day {
    font-size: 6vw;
  }
}
</style>
