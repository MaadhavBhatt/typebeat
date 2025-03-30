<template>
  <div id="app">
    <p class="speed-counter">{{ displayWPM }} WPM</p>
    <div class="history">
      <p class="history__heading">History</p>
      <p v-for="(wpm, index) in reversedWPMHistory" :key="index">
        {{ wpm }} WPM
      </p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      words: 0, // letters typed
      startTime: Date.now(),
      wpm: 0,
      wpmHistory: Array(10).fill(0),
      smoothedWPMHistory: Array(10).fill(0),
      displayWPM: 0,
    }
  },
  computed: {
    reversedWPMHistory() {
      return this.smoothedWPMHistory.slice().reverse();
    }
  },
  mounted() {
    document.addEventListener('keypress', () => {
      if (this.words === 0) this.startTime = Date.now();
      this.words++;
    });

    setInterval(() => {
      this.calculateWPM();
      let wpmHistory = this.wpmHistory.slice(-3);
      let smoothedWPM = this.smooth(wpmHistory)[wpmHistory.length - 1];

      this.smoothedWPMHistory.shift(Math.round(smoothedWPM));
      console.log(this.smooth(wpmHistory), wpmHistory);
      this.displayWPM = Math.round(smoothedWPM);
    }, 5000);
  },

  methods: {
    average(arr) {
      return arr.reduce((a, b) => a + b, 0) / arr.length;
    },

    smooth(values, windowSize = 3) {
      var smoothed = [];
      for (var i = 0; i < values.length; i++) {
        let window = [];
        for (let j = -Math.floor(windowSize / 2); j <= Math.floor(windowSize / 2); j++) {
          let index = i + j;
          if (index < 0) index = 0;
          if (index >= values.length) index = values.length - 1;
          window.push(values[index]);
        }
        smoothed.push(this.average(window));
      }
      return smoothed;
    },

    calculateWPM() {
      const timeElapsed = (Date.now() - this.startTime) / 1000 / 60; // from ms to minutes
      this.wpm = Math.round(this.words / 5 / timeElapsed); // 5 letters per word
      this.wpmHistory.shift(this.wpm);
      this.words = 0;
      this.startTime = Date.now();
    },
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100..900;1,100..900&display=swap');

/* Reset */
:root {
  --clr-light-1: #f0f0f0;
  --clr-dark-1: #333;

  font-size: 10px;
}

*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  min-height: 100vh;
  font-family: 'Roboto', sans-serif;
  line-height: 1.6;
  font-size: 1.6rem;
  font-weight: 400;
}

img {
  max-width: 100%;
  display: block;
}

button {
  font: inherit;
  border: none;
  cursor: pointer;
}

a {
  color: inherit;
  text-decoration: none;
}

ul {
  list-style: none;
}

#app {
  display: grid;
  place-items: center;
  min-height: 100vh;
  background-color: var(--clr-light-1);
}

.speed-counter {
  font-size: 5rem;
  font-weight: 500;
  color: var(--clr-dark-1);
}

.history {
  position: fixed;
  right: 2rem;
  top: 2rem;
  text-align: right;
  font-size: 2rem;
}

.history__heading {
  font-size: 2.5rem;
  font-weight: 500;
  color: var(--clr-dark-1);
}
</style>
