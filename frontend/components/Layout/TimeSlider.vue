<template>
  <v-slider
    :value="sliderValue"
    hide-details
    :max="runtime"
    validate-on-blur
    thumb-label
    :step="0"
    class="time-slider"
    @change="onPositionChange"
    @mousedown="onClick"
    @mouseup="onClick"
    @input="onInputChange"
  >
    <template #prepend>
      <span class="mt-1">
        {{ formatTime(playbackManager.currentTime) }}
      </span>
    </template>
    <template #thumb-label>
      {{ formatTime(sliderValue) }}
    </template>
    <template #append>
      <span class="mt-1">
        {{ formatTime(runtime) }}
      </span>
    </template>
  </v-slider>
</template>

<script lang="ts">
import Vue from 'vue';
import { mapStores } from 'pinia';
import { playbackManagerStore } from '~/store';
import { ticksToMs, formatTime } from '~/utils/time';

export default Vue.extend({
  data() {
    return {
      clicked: false,
      currentInput: 0
    };
  },
  computed: {
    ...mapStores(playbackManagerStore),
    runtime(): number {
      return (
        ticksToMs(this.playbackManager.getCurrentItem?.RunTimeTicks) / 1000 || 0
      );
    },
    sliderValue: {
      get(): number {
        if (!this.clicked) {
          return this.playbackManager.currentTime || 0;
        }

        return this.currentInput;
      }
    }
  },
  methods: {
    onPositionChange(value: number): void {
      if (!this.clicked) {
        this.playbackManager.changeCurrentTime(value);
      }
    },
    onInputChange(value: number): void {
      this.currentInput = value;
    },
    onClick(): void {
      this.currentInput = this.playbackManager.currentTime || 0;
      this.clicked = !this.clicked;
    },
    formatTime
  }
});
</script>

<style lang="scss">
.time-slider .v-slider__thumb-container,
.time-slider .v-slider__track-background,
.time-slider .v-slider__track-fill {
  transition: none !important;
}
</style>
