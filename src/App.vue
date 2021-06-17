<template>
  <div>
    <div class="work">
      <!-- <a style="color: white" href="#" @click="test">test</a> -->
      <div class="layer car-mode" :class="mode"></div>
      <!-- div class="layer check" v-if="checkEngine"></div>
      <div class="layer park" v-if="park"></div-->
      <div class="layer gearbox" :class="{parking: shift === 0, rear: shift === 1, neutral: shift === 2, drive: shift === 3, three: shift === 4}"></div>
      <div class="low-info gold turn" :class="{active: mode === 'front'}">TURN</div>
      <div class="low-info gold exhst"  :class="{active: mode === 'back'}">EXHST</div>
      <div class="low-info state turn-state">{{turn ? 'SEQUENCE' : 'NORMAL'}}</div>
      <div class="low-info state exhst-state">{{boost ? 'BOOST' : 'NORMAL'}}</div>
      <div class="door-info" v-if="checkEngine">DOOR</div>
      <div class="hood-info" v-if="park">HOOD</div>
      <div class="programming" v-if="mode === 'main'">HOLD <span class="gold">OK</span> TO SELECT</div>
      <!--div class="programming" v-if="mode === 'switch'">Press left or rigth button <br/>to select option</div-->
      <div class="programming select" v-if="mode === 'front'">TURN SIGNALS</div>
      <div class="programming select" v-if="mode === 'back'">EXHAUST</div>
      <div class="programming select" v-if="mode === 'turn-sel'">
        <div class="label">TURN SIGNALS</div>
        <div class="info">&nbsp;{{sturn ? 'SEQUENCE' : 'NORMAL'}}</div>
      </div>
      <div class="programming select" v-if="mode === 'exh-sel'">
        <div class="label">EXHAUST</div>
        <div class="info">&nbsp;{{sboost ? 'BOOST' : 'NORMAL'}}</div>
      </div>
      <div class="programming" v-if="mode === 'programming'">Set gearbox shift lever in position <br/>{{gears[shift]}} and press OK button</div>
      <div class="cross layer" v-if="check">
        <div class="vertical"></div>
        <div class="horizontal"></div>
      </div>
    </div>
  <div class="layers" v-if="loading">
    <div class="gif" v-if="gif">
      <img @load="gifStarted()" src="./assets/img/Start.gif">
    </div>
  </div>
  </div>
</template>

<script>


export default {
  name: 'App',
  data() {
    return {
      loading: true,
      gif: false,
      boost: false,
      turn: false,
      sboost: false,
      sturn: false,
      check: true,
      checkEngine: true,
      park: true,
      mode: 'main',
      cmode: 'def',
      shift: 0,
      gears: ['P', 'R', 'N', 'D', '3'],
      socket: {},
      okTimer: 0,
      timer: 0,
    }
  },
  created() {
    document.addEventListener('keydown', key => {
      //eslint-disable-next-line
      // console.log(key)
      switch(key.key) {
        case 'Enter': this.holdOk(); break
        case 'ArrowLeft': this.cLeft(); break
        case 'ArrowRight': this.cRight(); break
        case 'ArrowUp': this.cUp(); break
        case 'ArrowDown': this.cDown(); break
        case ' ': this.cOk(); break
        case 'p': this.mode = 'programming'; break
        case 'Escape': this.mode = 'main'; break
      }
    })

    //eslint-disable-next-line
    this.socket = io()
    this.socket.on('command', command => {
      switch(command) {
        case 'A': this.cUp(); break
        case 'B': this.cOk(); break
        case 'C': this.cRight(); break
        case 'D': this.cLeft(); break
        case 'E': this.cDown(); break
        case 'P': this.mode = 'programming'; this.shift = 0; break
        case 'M': this.mode = 'main'; break
        case 'Q': this.shift = 0; break
        case 'R': this.shift = 1; break
        case 'S': this.shift = 2; break
        case 'T': this.shift = 3; break
        case 'U': this.shift = 4; break      
      }
      //eslint-disable-next-line
      console.log(command)
    })
    // this.socket.on('response', response => {
    //   alert(response)
    // })

    this.socket.on('check', check => {
      //eslint-disable-next-line
      // console.log('check', check)
      if(check.toString() === '0') {
        this.checkEngine = false
      } else {
        this.checkEngine = true
      }
    })    
    this.socket.on('brake', brake => {
      //eslint-disable-next-line
      // console.log('brake', brake)
      if(brake.toString() === '0') {
        this.park = false
      } else {
        this.park = true
      }
    })  
    
  },
  mounted() {
        this.gif = true

        setTimeout(() => {
          this.gif = false
          this.loading = false
        }, 2000)

  // датчики
    this.socket.on('sconnect', res => {
      if(res == 'success') {
        this.gif = true

        setTimeout(() => {
          this.gif = false
          this.loading = false
        }, 2000)

        setInterval(() => {
          //eslint-disable-next-line
          console.log('reading')
          this.socket.emit('read', 'read')
        }, 500)
      }
    })

    this.mode = 'turn'
    setTimeout(() => {
      this.mode = 'back'
      setTimeout(() => this.mode = 'main', 500)
    }, 500)    
  },
  methods: {
    cUp() {
      switch(this.mode) {
        case 'turn-sel':
          this.sturn = !this.sturn
        break
        case 'exh-sel':
          this.sboost = !this.sboost
        break
      }
    },
    
    cDown() {
      switch(this.mode) {
        case 'turn-sel':
          this.sturn = !this.sturn
        break
        case 'exh-sel':
          this.sboost = !this.sboost
        break
      }
    },
    
    cLeft() {
      switch(this.mode) {
        case 'front': 
          this.mode = 'back'
        break
        case 'back': 
          this.mode = 'front'
        break
        case 'turn-sel':
          this.sturn = !this.sturn
        break
        case 'exh-sel':
          this.sboost = !this.sboost
        break
      }
    },
    
    cRight() {
      switch(this.mode) {
        case 'front': 
          this.mode = 'back'
        break
        case 'back': 
          this.mode = 'front'
        break
        case 'turn-sel':
          this.sturn = !this.sturn
        break
        case 'exh-sel':
          this.sboost = !this.sboost
        break
      }
    },

    holdOk() {
      this.mode = 'front'
    },
    
    cOk() {
      switch(this.mode) {
        case 'front':
          this.mode = 'turn-sel' 
        break
        case 'back': 
          this.mode = 'exh-sel' 
        break
        case 'turn-sel':
          this.turn = this.sturn
          this.mode = 'front'
        break
        case 'exh-sel':
          this.boost = this.sboost
          this.mode = 'back'
        break
      }

      // if(this.mode == 'programming') {
      //   if(this.shift < 4) {
      //     this.shift ++
      //   } else {
      //     this.mode = 'main'
      //   }
      // } else {
      //   if(this.mode === 'front') {
      //     this.mode = 'turn-sel'
      //   }
      // }
    },

    holdTime() {
      if(this.timer) {
        clearTimeout(this.timer)
      }
      this.timer = setTimeout(() => this.mode = 'main', 5000)
    },

    gifStarted() {
      //eslint-disable-next-line
      console.log('gif')
    },    
    test() {
      this.socket.emit('test', 'sdildfsjkdfgjkldfgjklfdsjk')
    }
  },
  watch: {
    boost(value) {
      if(this.mode !== 'programming') this.holdTime()
      if(value) {
        this.socket.emit('exhst', 'boost')
      } else {
        this.socket.emit('exhst', 'normal')
      }
    },
    turn(value) {
      if(this.mode !== 'programming') this.holdTime()
      if(value) {
        this.socket.emit('turn', 'blink')
      } else {
        this.socket.emit('turn', 'normal')
      }
    },
    mode() {
      console.log(this.mode)
      if(this.mode !== 'programming') this.holdTime()
    },

    sturn() {
      if(this.mode !== 'programming') this.holdTime()
    },
    sboost() {
      if(this.mode !== 'programming') this.holdTime()
    },
  }
  
}
</script>

<style>
@import 'assets/css/main.css';
</style>
