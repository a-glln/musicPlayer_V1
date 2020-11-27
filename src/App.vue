<template>
  <v-app dark>
    <!-- Panneau gauche -->
    <v-navigation-drawer app>
      <!-- BAR DE RECHERCHE -->
        <player-search-bar
        :playlist="playlist">
        </player-search-bar>
        <!-- Playlist -->
      <player-playlist-panel
      :playlist="playlist"
      :selectedTrack="selectedTrack"
      @selecttrack="selectTrack"
      @playtrack="play">
      </player-playlist-panel>
      <v-spacer></v-spacer>
      <router-view></router-view>
    </v-navigation-drawer>
    <!-- TitleBar -->
    <player-info-panel
    :trackInfo="getTrackInfo">
    </player-info-panel>
    <!-- content -->
    <v-content>
      <v-container fluid>
        <v-img
          width="30%"
          id="imgFond" src="src/assets/song.gif" aspect-ratio="2.75">
        </v-img>
          <player-controls-bars
          :progress="progress"
          @playtrack="play"
          @pausetrack="pause"
          @stoptrack="stop"
          @skiptrack="skip"
          @updateseek="setSeek">
          </player-controls-bars>
      </v-container>
    </v-content>
  </v-app>
</template>
<script>
  import PlayerPlaylistPanel from './components/PlayerPlaylistPanel.vue'
  import PlayerControlsBars from './components/PlayerControlsBars.vue'
  import PlayerInfoPanel from './components/PlayerInfoPanel.vue'
  import PlayerSearchBar from './components/PlayerSearchBar'
  import ArtistePage from './components/ArtistPage'
  import VueRouter from 'vue-router'

  export default {
    components: {
      PlayerPlaylistPanel,
      PlayerControlsBars,
      PlayerInfoPanel,
      PlayerSearchBar,
      ArtistePage
    },
    data () {
      return {
        playlist: [
          {title: "Roll Up", artist: "Wiz Khalifa", howl: null, display: true},
          {title: "Smoke Weed Everyday", artist: "Snoop dog ft. Dr Dre", howl: null, display: true},
          {title: "Stronger", artist: "Kanye west", howl: null, display: true},
          {title: "Palm trees", artist: "Flatbush Zombies", howl: null, display: true},
          {title: "La Source", artist: "1995", howl: null, display: true} 
        ],
        selectedTrack: null,
        index: 0,
        playing: false,
        loop: false,
        shuffle: false,
        seek: 0
      }
    },
    computed: {
      currentTrack () {
        return this.playlist[this.index]
      },
      progress () {
        if (this.currentTrack.howl.duration() === 0) return 0
        return this.seek / this.currentTrack.howl.duration()
      },
      getTrackInfo () {
        let artist = this.currentTrack.artist,
            title = this.currentTrack.title,
            seek = this.seek,
            duration = this.currentTrack.howl.duration()
        return {
          artist,
          title,
          seek,
          duration,
        }
      }
    },
    watch: {
      playing(playing) {
        this.seek = this.currentTrack.howl.seek()
        let updateSeek
        if (playing) {
          updateSeek = setInterval(() => {
            this.seek = this.currentTrack.howl.seek()
          }, 250)
        } else {
          clearInterval(updateSeek)
        }
      },
    },
    //Pour remplacer les "_" dans le nom des mp3 par des espaces
    created: function () {
      this.playlist.forEach( (track) => {
        let file = track.title.replace(/\s/g, "_")
        track.howl = new Howl({
          src: [`src/assets/playlist/${file}.mp3`],
          onend: () => {
            if (this.loop) {
              this.play(this.index)
            } else {
              this.skip('next')
            }
          }
        })
      })
    },
    methods: {
      selectTrack (track) {
        this.selectedTrack = track
      },
      play (index) {
        let selectedTrackIndex = this.playlist.findIndex(track => track === this.selectedTrack)

        if (typeof index === 'number') {
          index = index
        } else if (this.selectedTrack) {
          if (this.selectedTrack != this.currentTrack) {
            this.stop()
          }
          index = selectedTrackIndex
        } else {
          index = this.index
        }

        let track = this.playlist[index].howl

        if (track.playing()) {
          return
        } else {
          track.play()
        }
        
        this.selectedTrack = this.playlist[index]
        this.playing = true
        this.index = index
      },
      pause () {
        this.currentTrack.howl.pause()
        this.playing = false
      },
      stop () {
        this.currentTrack.howl.stop()
        this.playing = false
      },
      skip (direction) {
        let index = 0,
            lastIndex = this.playlist.length - 1
        if (this.shuffle) {
          index = Math.round(Math.random() * lastIndex)
          while (index === this.index) {
            index = Math.round(Math.random() * lastIndex)
          }
        } else if (direction === "next") {
          index = this.index + 1
          if (index >= this.playlist.length) {
            index = 0
          }
        } else {
          index = this.index - 1
          if (index < 0) {
            index = this.playlist.length - 1
          }
        }

        this.skipTo(index)
      },
      skipTo (index) {
        if (this.currentTrack) {
          this.currentTrack.howl.stop()
        }

        this.play(index)
      },
      setSeek (percents) {
        let track = this.currentTrack.howl

        if (track.playing()) {
          track.seek((track.duration() / 100) * percents)
        }
      }
    }
  }
</script>
