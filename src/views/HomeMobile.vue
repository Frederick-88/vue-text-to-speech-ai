<template>
  <!-- https://github.com/tjFogarty/speech-synthesis -->
  <form class="word-reader-container--mobile" @submit.prevent="startAi">
    <h2 class="title">Word Reader</h2>

    <div class="controller-control">
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
    </div>

    <textarea
      class="textarea-input"
      id="word-input"
      type="text"
      v-model="wordInput"
      rows="10"
      required
    />

    <div class="footer-controller">
      <button
        type="button"
        class="controller controller--sound-type"
        :class="{ 'is-selected': selectedVoice === 'woman' }"
        @click="selectVoice('woman')"
      >
        Woman Voice
      </button>
      <button
        type="button"
        class="controller controller--sound-type"
        :class="{ 'is-selected': selectedVoice === 'man' }"
        @click="selectVoice('man')"
      >
        Man Voice
      </button>
    </div>
  </form>
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
      this.pauseAi();
      this.selectedVoice = type;
    },

    /**
     * Pause the AI of text-to-speech
     */
    pauseAi() {
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
.word-reader-container--mobile {
  min-width: 100vw;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  background: #185070;
  padding: 0 40px;

  .title {
    font-size: 2.5rem;
    margin: 0 0 30px;
    font-weight: 400;
    color: white;
  }

  .controller-control {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
  }

  .footer-controller {
    width: 100%;
    display: flex;
    align-items: center;
    flex-direction: column;
  }

  .controller {
    outline: 0;
    border: 0;
    padding: 7px 15px;
    width: 120px;
    height: 45px;
    background: white;
    color: #185070;
    font-size: 1.125rem;
    border-radius: 8px;
    cursor: pointer;
    transition: filter 0.5s cubic-bezier(0.075, 0.82, 0.165, 1);

    &.controller--sound-type {
      width: 200px;
      margin-bottom: 25px;

      &:last-of-type {
        margin-bottom: 0;
      }
    }

    &.is-selected {
      background: #2a74e3;
      color: white;
    }

    &.is-disabled {
      background: #cccccc;
      color: #666666;
      pointer-events: none;
    }
  }

  .textarea-input {
    width: 100%;
    border-radius: 20px;
    height: 250px;
    padding: 20px;
    margin: 55px 0 50px;
  }
}
</style>
