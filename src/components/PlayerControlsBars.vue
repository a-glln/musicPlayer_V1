<template>
  <div>
    <v-toolbar height="40">
      <v-progress-linear id="sliderSong" height="40" v-model="trackProgress"  @click="updateSlider($event)"></v-progress-linear> 
    </v-toolbar>
    <v-card class="mx-auto">
      <v-card-text> 
            <v-btn text icon @click="toggleMute">
              <template v-if="!this.muted">
                <v-icon v-if="this.volume >= 0.5">mdi-volume-plus</v-icon>
                <v-icon v-else-if="this.volume > 0">mdi-volume-minus</v-icon>
                <v-icon v-else>mdi-volume-mute</v-icon>
              </template>
              <v-icon v-show="this.muted">mdi-volume-off</v-icon>
            </v-btn>
        <v-slider id="slider" v-model="volume" @input="updateVolume(volume)" max="1" step="0.05"></v-slider>
        {{ this.volume * 100 + '%' }}
      </v-card-text>
    </v-card>
    <v-spacer></v-spacer><br>
    <v-card class="mx-auto">   
      <v-card-text>
        <div style="margin:auto;display: table;">     
          <v-btn outlined fab small color="light-blue" @click="skipTrack('prev')">
            <v-icon>skip_previous</v-icon>
          </v-btn>
          <v-btn outlined fab small color="light-blue" @click="stopTrack">
            <v-icon>stop</v-icon>
          </v-btn>
          <v-btn outlined fab color="light-blue" @click="playTrack()">
            <v-icon large>play_arrow</v-icon>
          </v-btn>
          <v-btn outlined fab small color="light-blue" @click="pauseTrack">
            <v-icon>pause</v-icon>
          </v-btn>
          <v-btn outlined fab small color="light-blue" @click="skipTrack('next')">
            <v-icon>skip_next</v-icon> 
          </v-btn>
        </div>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
  export default {
    props: {
      loop: Boolean,
      shuffle: Boolean,
      progress: Number
    },
    data () {
      return {
        volume: 0.3,
        muted: false
      }
    },
    computed: {
      trackProgress () {
        return this.progress * 100
      },
    },
    created: function () {
      Howler.volume(this.volume)
    },
    methods: {
      playTrack(index) {
        this.$emit('playtrack', index)
      },
      pauseTrack() {
        this.$emit('pausetrack')
      },
      stopTrack() {
        this.$emit('stoptrack')
      },
      skipTrack (direction) {
          this.$emit('skiptrack', direction)
      },
      updateVolume (volume) {
        Howler.volume(volume)
      },
      toggleMute () {
        Howler.mute(!this.muted)
        this.muted = !this.muted
      },
      updateSlider (event) {
        let el = document.querySelector("#sliderSong"),
            mousePos = event.offsetX,
            elWidth = el.clientWidth,
            percents = (mousePos / elWidth) * 100
        this.$emit('updateseek', percents)
      }
    } 
  }
</script>
<style type="css">
#imgFond{
  margin: 50px auto 70px auto;
}
#slider{
  width: 100%;
}
#toolbar{
  margin: auto;
}
</style>