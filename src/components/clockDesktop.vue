<template>
  <section class="clock" ref="clock">
    <div class="hour">{{ time|date2HHMM }}</div>
    <div class="day">{{ time|date2DDMM }}</div>
  </section>
</template>

<script>
export default {
  name: 'clockDesktop',
  props: ['time'],
  mounted() {
    this.emitResize()
  },
  updated() {
    this.emitResize()
  },
  filters: {
    date2HHMM(d) {
      const options = { hour: 'numeric', minute: 'numeric'}
      // , second: 'numeric'
      // hour12: false,
      return new Intl.DateTimeFormat('es-ES', options).format(d)
    },
    date2DDMM(d) {
      const options = { day: 'numeric', weekday: 'short', month: 'short' }
      return new Intl.DateTimeFormat('es-ES', options).format(d)
    }
  },
  methods: {
    emitResize() {
      this.$emit('resize', {
        width: this.$refs.clock.clientWidth,
        height: this.$refs.clock.clientHeight
      })
    }
  }
}
</script>
