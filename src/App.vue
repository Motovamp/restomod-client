<template>
  <div>
    <div class="work">
      <!-- <a style="color: white" href="#" @click="test">test</a> -->
      <div class="layer car-mode" :class="mode"></div>
      <div class="layer check" v-if="checkEngine"></div>
      <div class="layer park" v-if="checkEngine"></div>
      <div class="layer gearbox" :class="{parking: shift === 0, rear: shift === 1, neutral: shift === 2, drive: shift === 3, three: shift === 4}"></div>
      <div class="low-info gold turn">TURN</div>
      <div class="low-info gold exhst">EXHST</div>
      <div class="low-info turn-state">{{turn ? 'BLINK' : 'NORMAL'}}</div>
      <div class="low-info exhst-state">{{boost ? 'BOOST' : 'NORMAL'}}</div>
      <div class="cross layer" v-if="check">
        <div class="vertical"></div>
        <div class="horizontal"></div>
      </div>
    </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  data() {
    return {
      boost: null,
      check: true,
      checkEngine: true,
      mode: 'main',
      shift: 0,
      socket: null,
      turn: null,
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
