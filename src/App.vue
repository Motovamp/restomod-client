<template>
  <div>
    <div class="work">
      <!-- <a style="color: white" href="#" @click="test">test</a> -->
      <div class="layer car-mode" :class="mode"></div>
      <div class="layer check" v-if="checkEngine"></div>
      <div class="layer park" v-if="checkEngine"></div>
      <div class="layer gearbox" :class="{parking: shift === 0, rear: shift === 1, neutral: shift === 2, drive: shift === 3, three: shift === 4}"></div>
      <div class="low-info gold turn" :class="{active: mode === 'front'}">TURN</div>
      <div class="low-info gold exhst"  :class="{active: mode === 'back'}">EXHST</div>
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
      boost: false,
      check: false,
      checkEngine: true,
      mode: 'main',
      cmode: 'def',
      shift: 0,
      socket: null,
      turn: null,
    }
  },
  created() {
    //eslint-disable-next-line
    this.socket = io()
    this.socket.on('command', command => {
      switch(command) {
        case 'A': this.cUp(); break
        case 'B': this.cOk(); break
        case 'C': this.cRight(); break
        case 'D': this.cLeft(); break
        case 'E': this.cDown(); break
        case 'Q': this.shift = 0; break
        case 'R': this.shift = 1; break
        case 'S': this.shift = 2; break
        case 'T': this.shift = 3; break
        case 'U': this.shift = 4; break
        
      }
      //eslint-disable-next-line
      console.log(command)
    })
    this.socket.on('response', response => {
      alert(response)
    })
  },
  methods: {
    cUp() {
      if(this.mode === 'front') {
        this.turn = !this.turn
      }
      if(this.mode === 'back') {
        this.boost = !this.boost
      }
    },
    
    cDown() {
      if(this.mode === 'front') {
        this.turn = !this.turn
      }
      if(this.mode === 'back') {
        this.boost = !this.boost
      }
    },
    
    cLeft() {
      this.mode = 'front'
    },
    
    cRight() {
      this.mode = 'back'
    },
    
    cOk() {

    },
    
    test() {
      this.socket.emit('test', 'sdildfsjkdfgjkldfgjklfdsjk')
    }
  },
  
}
</script>

<style>
@import 'assets/css/main.css';
</style>
