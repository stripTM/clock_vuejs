<template>
  <section class="clock" ref="clock">
    <div class="hour portrait">
      <span class="digit">{{ time|date2Hour }}</span>
      <span class="digit">{{ time|date2Minute }}</span>
    </div>
    <div class="hour landscape">{{ time|date2HourMinute }}</div>
    <div class="day">{{ time|date2DayMonth }}</div>
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
    date2Hour(d) {
      const hour = d.getHours()
      return hour < 10 ? `0${hour}` : hour
    },
    date2Minute(d) {
      const minute = d.getMinutes()
      return minute < 10 ? `0${minute}` : minute
    },
    date2HourMinute(d) {
      const options = { hour: 'numeric', minute: 'numeric'}
      // hour12: false,
      return new Intl.DateTimeFormat('es-ES', options).format(d)
    },
    date2DayMonth(d) {
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
