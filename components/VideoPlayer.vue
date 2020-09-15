<template>
  <video
    ref="video"
    class="video"
    :src="srcComputed"
    :controls="true"
    :autoplay="autoplay"
    :playsinline="playsinline"
    @play="play"
    @pause="pause"
    @click="atPlayPause"
    @ended="atEnded"
    @timeupdate="atTimeupdate"
    @volumechange="atVolumechange"
  />
</template>
<script>
export default {
  name: 'VideoPlayer',
  props: {
    src: {
      required: true,
      type: [String, Array],
    },
    /**
     * set the video to autoplay as it's loaded
     */
    autoplay: {
      type: Boolean,
      default: false,
    },
    /**
     * show/hide the controls
     */
    controls: {
      type: Boolean,
      default: undefined,
    },
    /**
     * set the video to playsinline as it's loaded
     */
    playsinline: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      showSound: false,
      mouseover: false,
      playing: false,
      started: false,
      loading: false,
      time: 0,
      duration: 0,
      volumeInternal: 1,
      fullscreenInternal: false,
    }
  },
  computed: {
    /**
     * @private
     */
    srcComputed() {
      if (typeof this.src === 'string') return this.src
      return null
    },

    /**
     * @private
     */
    playingComputed: {
      get() {
        if (this.value !== undefined) return this.value

        return this.playing
      },

      set(e) {
        if (this.value !== undefined) {
          this.$emit('input', e)
        } else {
          this.playing = e
        }
      },
    },
  },

  watch: {
    value(after) {
      if (after && this.$refs.video.paused) {
        this.$refs.video.play()
      } else if (!after && !this.$refs.video.paused) {
        this.$refs.video.pause()
      }
    },

    volume() {
      this.$refs.video.volume = this.volumeComputed
    },
  },
  methods: {
    /**
     * @private
     */
    atEnded() {
      console.log('video ended')
    },
    /**
     * @private
     */
    atVolumechange() {
      this.volume = this.$refs.video.volume
      console.log('volume changed')
    },
    /**
     * @private
     */
    atTimeupdate() {
      this.time = this.$refs.video.currentTime
      console.log('time updated', this.time)
    },
    /**
     * @private
     */
    atPlayPause() {
      if (!this.playingComputed) this.$refs.video.play()
      else this.$refs.video.pause()
    },
    /**
     * @private
     */
    play() {
      this.playingComputed = true
      this.started = true
      console.log('Called play')
    },

    /**
     * @private
     */
    pause() {
      if (this.time === 0) return
      this.playingComputed = false
      console.log('Called pause')
    },
  },
}
</script>
<style>
video.video {
  width: 100%;
}
</style>