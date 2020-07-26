<template>
  <div>
    <form @submit.prevent="submitForm">
      <input type="file" @change="fileChange" accept="audio/*">
      <button type="submit">Use Music</button>
    </form>
    <div class="control">
      <span>Speed: {{speed}}x</span>
      <vue-slider v-model="speed" :min="0.1" :max="4" :interval="0.1"/>
      <span>Repeat:</span>
      <input type="number" v-model="repeat">
      {{duration}} minutes
    </div>
    <button @click="play">Play</button>
    <button @click="stop">Stop</button>
  </div>
</template>

<script>
import { Howl } from 'howler';
import VueSlider from 'vue-slider-component';
import 'vue-slider-component/theme/antd.css';

export default {
  name: 'LocalPlayer',
  data() {
    return {
      localFile: null,
      howl: null,
      speed: 1,
      repeat: 1,
      currentRepeat: 0,
    };
  },
  computed: {
    duration() {
      return (this.howl ? this.howl.duration() : 0) / this.speed * this.repeat / 60;
    },
  },
  methods: {
    fileChange(event) {
      if (event.target.files.length > 0) {
        // eslint-disable-next-line prefer-destructuring
        this.localFile = event.target.files[0];
      } else {
        this.localFile = null;
      }
    },
    submitForm() {
      if (this.localFile) {
        const reader = new FileReader();
        reader.onload = () => {
          const data = reader.result;
          const decodedData = btoa(data);
          const format = this.localFile.name.split('.')
            .pop()
            .toLowerCase();
          const vm = this;
          this.$set(this, 'howl', new Howl({
            src: `data:audio/${format};base64,${decodedData}`,
            format,
            onend: function onend() {
              vm.currentRepeat += 1;
              if (vm.currentRepeat >= vm.repeat) {
                vm.howl.stop();
              }
            },
          }));
        };
        reader.readAsBinaryString(this.localFile);
      }
    },
    play() {
      if (this.repeat) {
        this.howl.rate(this.speed);
        this.howl.loop(this.repeat > 1);
        this.currentRepeat = 0;
        this.howl.play();
      }
    },
    stop() {
      this.howl.stop();
    },
  },
  components: {
    VueSlider,
  },
};
</script>

<style lang="scss">
  .control {
    max-width: 300px;
  }
</style>
