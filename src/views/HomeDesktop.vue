<template>
  <!-- https://github.com/tjFogarty/speech-synthesis -->
  <div class="word-reader-container--desktop">
    <form class="word-reader-form" @submit.prevent="startAi">
      <h2 class="title">Word Reader</h2>

      <div class="controller-container">
        <button
          type="button"
          class="controller controller--sound-type"
          :class="{ 'is-selected': selectedVoice === 'woman' }"
          @click="selectVoice('woman')"
        >
          Woman Voice
        </button>
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
        <button
          type="button"
          class="controller controller--sound-type"
          :class="{ 'is-selected': selectedVoice === 'man' }"
          @click="selectVoice('man')"
        >
          Man Voice
        </button>
      </div>

      <div class="content-container">
        <div class="radiobox-container">
          <h4 class="radiobox-title">Playback Speed</h4>
          <div class="rates-radiobox-list">
            <div class="radio-list-item-container">
              <input
                class="radio-list-item"
                v-model="selectedSpeed"
                type="radio"
                name="0.5"
                id="0.5"
                :value="0.5"
              />
              <label class="radio-list-label" for="0.5"> 0.5 </label>
            </div>
            <div class="radio-list-item-container">
              <input
                class="radio-list-item"
                v-model="selectedSpeed"
                type="radio"
                name="0.75"
                id="0.75"
                :value="0.75"
              />
              <label class="radio-list-label" for="0.75"> 0.75 </label>
            </div>
            <div class="radio-list-item-container">
              <input
                class="radio-list-item"
                v-model="selectedSpeed"
                type="radio"
                name="1"
                id="1"
                :value="1"
              />
              <label class="radio-list-label" for="1"> 1.0 </label>
            </div>
            <div class="radio-list-item-container">
              <input
                class="radio-list-item"
                v-model="selectedSpeed"
                type="radio"
                name="1.25"
                id="1.25"
                :value="1.25"
              />
              <label class="radio-list-label" for="1.25"> 1.25 </label>
            </div>
            <div class="radio-list-item-container">
              <input
                class="radio-list-item"
                v-model="selectedSpeed"
                type="radio"
                name="1.5"
                id="1.5"
                :value="1.5"
              />
              <label class="radio-list-label" for="1.5"> 1.5 </label>
            </div>
          </div>
        </div>

        <textarea
          class="textarea-input"
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
.word-reader-container--desktop {
  min-width: 100vw;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #185070;

  .word-reader-form {
    width: 80vw;
    height: 80vh;
    border: 4px solid #e0f4ff;
    border-radius: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    text-align: center;
    padding: 30px 80px;

    .title {
      font-size: 3rem;
      margin: 35px 0;
      font-weight: 400;
      color: white;
    }
  }

  .controller-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;

    .controller {
      outline: 0;
      border: 0;
      padding: 7px 15px;
      width: 160px;
      height: 50px;
      background: white;
      color: #185070;
      font-size: 1.125rem;
      border-radius: 8px;
      margin: 0 15px;
      cursor: pointer;
      transition: filter 0.5s cubic-bezier(0.075, 0.82, 0.165, 1);

      &.controller--sound-type {
        width: 220px;
        margin: 0;
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

      &:hover {
        filter: brightness(0.85);
      }
    }
  }

  .content-container {
    display: flex;
    justify-content: space-between;
    width: 100%;
    margin: 80px 0 30px;

    .radiobox-container {
      width: 20%;

      .radiobox-title {
        font-size: 1.25rem;
        margin-bottom: 20px;
        margin-top: 0;
        font-weight: 400;
        color: white;
        text-align: left;
      }

      .rates-radiobox-list {
        color: white;

        .radio-list-item-container {
          display: flex;
          align-items: center;
          margin-bottom: 8px;

          &:last-of-type {
            margin-bottom: 0;
          }
        }

        .radio-list-item {
          cursor: pointer;
          display: none;
        }

        .radio-list-label {
          font-size: 1rem;
          margin-left: 8px;
          cursor: pointer;

          &:hover {
            &:before {
              transition: filter 0.5s cubic-bezier(0.075, 0.82, 0.165, 1);
              filter: brightness(0.65);
            }
          }
        }

        .radio-list-label:before {
          content: " ";
          display: inline-block;
          position: relative;
          top: 5px;
          left: -12px;
          width: 15px;
          height: 15px;
          border-radius: 50%;
          border: 2px solid white;
          background-color: white;
        }

        .radio-list-item:checked + .radio-list-label:before {
          content: " ";
          display: inline-block;
          position: relative;
          border: 4px solid white;
          border-radius: 50%;
          width: 11px;
          height: 11px;
          top: 5px;
          left: -12px;
          background-color: #2a74e3;
          filter: brightness(1);
        }
      }
    }

    .textarea-input {
      width: 80%;
      margin-left: 50px;
      border-radius: 20px;
      height: 100%;
      padding: 20px;
    }
  }
}
</style>
