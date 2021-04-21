<template>
  <div>
    <div class="work">
      <!-- <a style="color: white" href="#" @click="test">test</a> -->
      <div class="layer car-mode" :class="mode"></div>
      <div class="layer gearbox" :class="{parking: shift === 0, rear: shift === 1, neutral: shift === 2, drive: shift === 3, three: shift === 4}"></div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  data() {
    return {
      mode: 'main',
      shift: 0,
      socket: null,

    }
  },
  created() {
    //eslint-disable-next-line
    this.socket = io()
    this.socket.on('response', response => {
      alert(response)
    })
  },
  methods: {
    test() {
      this.socket.emit('test', 'sdildfsjkdfgjkldfgjklfdsjk')
    }
  },
  
}
</script>

<style>
@import 'assets/css/main.css';
</style>
