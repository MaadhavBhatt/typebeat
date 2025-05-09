<template>
  <div class="app">
    <p class="speed-counter">{{ displayWPM }} WPM</p>
    <div class="history">
      <p v-if="recentWPMHistory.length !== 0" class="history__heading">History</p>
      <p v-for="(wpm, index) in recentWPMHistory" :key="index">
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
      wpmHistory: [],
      smoothedWPMHistory: [],
      displayWPM: 0,
    }
  },
  computed: {
    recentWPMHistory() {
      return this.smoothedWPMHistory.slice().reverse().slice(0, 5);
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

      this.smoothedWPMHistory.push(Math.round(smoothedWPM));
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
      this.wpmHistory.push(this.wpm);
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
  --clr-light-1: hsl(0, 0%, 94%);
  --clr-dark-1: hsl(0, 0%, 20%);
  /* #333 */
  --clr-dark-2: hsl(0, 0%, 30%);

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
  font-weight: 300;
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

.app {
  display: grid;
  place-items: center;
  grid-template-rows: 1fr auto;
  min-height: 100vh;
  background-color: var(--clr-light-1);
  padding: 2rem 0;
}

.speed-counter {
  font-size: 5rem;
  font-weight: 500;
  color: var(--clr-dark-1);
}

.history {
  font-size: 2rem;
  text-align: center;
  color: var(--clr-dark-2);
}

.history__heading {
  font-size: 2.5rem;
  font-weight: 400;
}

@media (min-width: 768px) {
  .history {
    position: fixed;
    right: 2rem;
    top: 2rem;
    text-align: right;
  }
}
</style>
