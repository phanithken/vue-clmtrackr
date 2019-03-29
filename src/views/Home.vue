<template>
  <div class="home">
    <video ref="v" width="300" height="300"></video>
    <input ref="b" type="button" @click="takePic" value="Take Picture" />
    <canvas ref="c" width="300" height="300"></canvas>
    <canvas style="position: absolute; right: 31.22em;" ref="o" width="300" height="300"></canvas>
  </div>
</template>

<script>
export default {
  name: 'home',
  data: function () {
    return {
      video: null,
      canvas: null,
      button: null,
      ctracker: null
    }
  },
  mounted: function () {
    navigator.mediaDevices.getUserMedia({ video: true })
      .then(mediaStream => {
        this.video = this.$refs.v
        this.canvas = this.$refs.c
        this.button = this.$refs.b
        
        this.video.srcObject = mediaStream
        this.video.play()
      })
      .catch(error => console.error('getUserMedia() error:', error))

      this.ctracker = new clm.tracker({
        searchWindow: 15,
        stopOnConvergence: true
      })
      this.ctracker.init()
      this.ctracker.start(this.$refs.v)
  },
  methods: {
    takePic: function () {
      this.ctracker.getCurrentPosition()
      this.canvas.getContext("2d").drawImage(this.video, 0, 0, this.canvas.width, this.canvas.height)
      var img = this.canvas.toDataURL("image/png")
      
      var canvasInput = this.$refs.o
      var cc = canvasInput.getContext('2d')

      cc.clearRect(0, 0, canvasInput.width, canvasInput.height)

      cc.clearRect(0, 0, canvasInput, canvasInput.height)
      this.ctracker.draw(canvasInput)
    }
  }
}
</script>
