<template>
  <!-- https://github.com/tjFogarty/speech-synthesis -->
  <div class="word-reader-form">
    <form @submit.prevent="startAi">
      <h1>Word Reader</h1>

      <div class="controller-container">
        <button
          type="button"
          class="controller"
          :class="{ 'is-selected': selectedVoice === 'woman' }"
          @click="selectVoice('woman')"
        >
          Woman Voice
        </button>
        <button
          type="submit"
          class="controller"
          :class="{ 'is-disabled': isPlaying }"
        >
          Play
        </button>
        <button
          type="button"
          class="controller"
          :class="{ 'is-disabled': !isPlaying }"
          @click="pauseAi"
        >
          Pause
        </button>
        <button
          type="button"
          class="controller"
          :class="{ 'is-selected': selectedVoice === 'man' }"
          @click="selectVoice('man')"
        >
          Man Voice
        </button>
      </div>

      <div class="content-container">
        <div class="rates-radiobox-list">
          <input v-model="selectedSpeed" type="radio" name="0.25" id="0.25" :value="0.25"> 0.25
          <input v-model="selectedSpeed" type="radio" name="0.5" id="0.5" :value="0.5"> 0.5
          <input v-model="selectedSpeed" type="radio" name="0.75" id="0.75" :value="0.75"> 0.75
          <input v-model="selectedSpeed" type="radio" name="1" id="1" :value="1"> 1.0
          <input v-model="selectedSpeed" type="radio" name="1.25" id="1.25" :value="1.25"> 1.25
          <input v-model="selectedSpeed" type="radio" name="1.5" id="1.5" :value="1.5"> 1.5
        </div>

        <textarea
          class="form-control"
          id="word-input"
          type="text"
          v-model="wordInput"
          rows="10"
          required
        />
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      wordInput: "",
      selectedVoice: "woman",
      selectedSpeed: 1,
      speechSynthesis: window.speechSynthesis,
      voiceList: [],
      wordReaderAi: new window.SpeechSynthesisUtterance(),

      isPlaying: false,
    };
  },
  mounted() {
    this.voiceList = this.speechSynthesis.getVoices(); // to support firefox browser

    if (this.voiceList.length) {
      console.log("voicelist loaded", this.voiceList);
    }

    // to support chrome browser
    this.speechSynthesis.onvoiceschanged = () => {
      this.voiceList = this.speechSynthesis.getVoices();
      console.log("voices are ready to use.");
    };

    this.listenForSpeechEvents();
  },
  methods: {
    /**
     * React to speech events
     */
    listenForSpeechEvents() {
      this.wordReaderAi.onstart = () => {
        console.log("on-start");
        this.isPlaying = true;
      };

      this.wordReaderAi.onend = () => {
        console.log("on-end");
        this.isPlaying = false;
      };
    },

    /**
     * Selecting type of voice (man / woman)
     */
    selectVoice(type) {
      this.selectedVoice = type;
    },

    /**
     * Pause the AI of text-to-speech
     */
    pauseAi(type) {
      this.speechSynthesis.cancel();
    },

    /**
     * Run the AI of text-to-speech with selected controls
     */
    startAi() {
      console.log("AI works");
      const voiceToIndex = this.selectedVoice === "woman" ? 2 : 3; // only get the UK versions of female & male

      this.wordReaderAi.text = this.wordInput;
      this.wordReaderAi.voice = this.voiceList[voiceToIndex];
      this.wordReaderAi.rate = this.selectedSpeed;
      this.speechSynthesis.speak(this.wordReaderAi);
    },
  },
};
</script>

<style lang="scss">
.word-reader-form {
  min-width: 100vw;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;

  .controller-container {
    display: flex;
    align-items: center;

    .controller {
      outline: 0;
      border: 0;
      padding: 7px 15px;
      background: white;
      color: #185070;
      font-size: 1.125rem;
      border-radius: 4px;
      margin: 0 15px;
      cursor: pointer;
      transition: filter 0.3s cubic-bezier(0.075, 0.82, 0.165, 1);

      &.is-selected {
        background: #2a74e3;
        color: white;
      }

      &.is-disabled {
        background: #cccccc;
        color: #666666;
        pointer-events: none;
      }

      &:hover {
        filter: brightness(0.85);
      }
    }
  }
}
</style>
