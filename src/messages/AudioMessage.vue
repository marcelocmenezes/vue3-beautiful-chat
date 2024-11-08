<script>
export default {
  props: {
    data: {
      type: Object,
      required: true
    },
    messageColors: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      seekValue: 0
    }
  },
  mounted() {
    const audio = this.$refs.audio

    audio.addEventListener('play', () => {
      this.$refs['audio-player'].classList.add('playing')
    })

    audio.addEventListener('pause', () => {
      this.$refs['audio-player'].classList.remove('playing')
    })

    audio.addEventListener('timeupdate', () => {
      this.seekValue = (audio.currentTime / audio.duration) * 100

      this.$refs['audio-player'].style.setProperty(
        '--player-current-time',
        `'${this.formatTimeToDisplay(audio.currentTime)}'`
      )
    })

    audio.addEventListener('loadeddata', () => {
      // this.$refs['audio-player'].classList.remove('loading')
      this.$refs['audio-player'].style.setProperty(
        '--player-current-date-time',
        `'${this.formatTimeToDisplay(audio.duration)}'`
      )
    })

    audio.addEventListener('ended', () => (audio.currentTime = 0))
  },
  methods: {
    togglePlayPause() {
      const audio = this.$refs.audio
      audio.paused ? audio.play() : audio.pause()
    },

    handleSlider(e) {
      const audio = this.$refs.audio
      const {duration} = audio
      const percent = e.target.value
      const currentTimeInSeconds = ((percent * duration) / 100).toFixed(2)
      audio.currentTime = currentTimeInSeconds
    },

    formatTimeToDisplay(seconds) {
      const milliseconds = seconds * 1000
      return new Date(milliseconds).toISOString().substr(14, 5)
    }
  }
}
</script>

<template>
  <div ref="audio-player" class="audio-player" :style="messageColors">
    <div class="player">
      <button type="button" class="btn-play" @click="togglePlayPause">
        <span class="material-icons icon-play">play_arrow</span>
        <span class="material-icons icon-pause">pause</span>
        <span class="material-icons icon-loop">loop</span>
      </button>
      <div class="timeline">
        <div class="line">
          <input
            ref="slider"
            v-model="seekValue"
            dir="ltr"
            type="range"
            min="0"
            max="100"
            :style="messageColors"
            @input="handleSlider"
          />
        </div>
        <div class="data">
          <div class="current-time"></div>
          <div class="time"></div>
        </div>
      </div>
    </div>
    <audio ref="audio" :src="data.file.url"></audio>
  </div>
</template>

<style>
.audio-player {
  --player-color-featured: #00e5c0;
  --player-color-background: #262d31;
  --player-color-text: #c5c6c8;
  --player-percent-played: 0;
  --player-current-time: '00:00';
  --player-current-date-time: '00:00';

  background: var(--player-color-background);
  display: inline-flex;
  min-width: 240px;
  width: 336px;
  max-width: 100%;
  border-radius: 0.4rem;
  padding: 0.4rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
  user-select: none;
  font-family: Arial, sans-serif;
}
.audio-player + .audio-player {
  margin-top: 1rem;
}

.audio-player audio {
  display: none;
}

.audio-player .player {
  flex: 1;
  display: flex;
}

.audio-player .player .btn-play {
  outline: none;
  appearance: none;
  cursor: pointer;
  background: none;
  border: 0;
  padding: 0 0.8rem 0 0.4rem;
}

.audio-player .player .btn-play:disabled {
  cursor: default;
}

.audio-player .player .btn-play span {
  color: var(--player-color-text);
  font-size: 38px;
  opacity: 0.8;
}

.audio-player .player .btn-play span:not(.icon-play),
.audio-player.playing .player .btn-play span:not(.icon-pause),
.audio-player.loading .player .btn-play span:not(.icon-loop) {
  display: none;
}

.audio-player.playing .player .btn-play .icon-pause {
  display: inline-block;
}

@keyframes load {
  to {
    transform: rotate(360deg);
  }
}

.audio-player.loading .player .btn-play {
  pointer-events: none;
}

.audio-player.loading .player .btn-play span {
  animation: load 1s linear infinite;
}

.audio-player.loading .player .btn-play .icon-loop {
  display: inline-block;
}

.audio-player .player .timeline {
  flex: 1;
  display: flex;
  flex-direction: column;
  position: relative;
  padding-bottom: 0.2rem;
}

.audio-player .player .timeline .line {
  --line-height: 0.24rem;

  flex: 1;
  display: flex;
  align-items: center;
  position: relative;
}

.audio-player .player .timeline .line:before {
  content: '';
  width: var(--player-percent-played);
  position: absolute;
  background: var(--player-color-featured);
  height: var(--line-height);
  border-radius: calc(var(--line-height) / 2);
}

.audio-player .player .timeline .line input[type='range'] {
  flex: 1;
  all: unset;
  appearance: none;
  background-color: initial !important;
  border: none;
  outline: none;
  width: 100%;
  position: relative;
}

.audio-player .player .timeline .line input[type='range']::-webkit-slider-thumb {
  appearance: none;
  background: var(--player-color-featured);
  width: 0.9rem;
  height: 0.9rem;
  border-radius: 50%;
  margin-top: calc(var(--line-height) * -1.4);
}

.audio-player .player .timeline .line input[type='range']::-moz-range-thumb {
  unset: all;
  appearance: none;
  border: 0;
  background: var(--player-color-featured);
  width: 0.9rem;
  height: 0.9rem;
  border-radius: 50%;
  margin-top: calc(var(--line-height) * -1.4);
}

.audio-player .player .timeline .line input[type='range']::-ms-thumb {
  appearance: none;
  background: var(--player-color-featured);
  width: 0.9rem;
  height: 0.9rem;
  border-radius: 50%;
  margin-top: calc(var(--line-height) * -1.4);
}

.audio-player .player .timeline .line input[type='range']::-webkit-slider-runnable-track {
  background: rgba(255, 255, 255, 0.2);
  height: var(--line-height);
  border-radius: calc(var(--line-height) / 2);
}

.audio-player .player .timeline .line input[type='range']::-moz-range-track {
  background: rgba(255, 255, 255, 0.2);
  height: var(--line-height);
  border-radius: calc(var(--line-height) / 2);
}

.audio-player .player .timeline .line input[type='range']::-ms-track {
  background: rgba(255, 255, 255, 0.2);
  height: var(--line-height);
  border-radius: calc(var(--line-height) / 2);
}

.audio-player .player .timeline .data {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 0.68rem;
  color: var(--player-color-text);
  position: absolute;
  width: 100%;
  bottom: 0;
}

.audio-player .player .timeline .data .current-time::before {
  content: var(--player-current-time);
}

.audio-player .player .timeline .data .time {
  display: flex;
  align-items: center;
}

.audio-player .player .timeline .data .time::before {
  content: var(--player-current-date-time);
}

.audio-player .player .timeline .data .time span {
  font-size: 1rem;
  margin-left: 0.4rem;
  color: var(--player-color-featured);
}
</style>
