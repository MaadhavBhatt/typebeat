<template>
  <div id="app">
    <p class="speed-counter">{{ wpm }} WPM</p>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  name: 'App',
  data() {
    return {
      words: 0, // letters typed
      startTime: Date.now(),
      wpm: 0,
      wpmHistory: [],
    }
  },
  mounted() {
    const speedCounter = document.querySelector('.speed-counter');

    document.addEventListener('keypress', () => {
      if (this.words === 0) this.startTime = Date.now();
      this.words++;
    });

    setInterval(() => {
      this.calculateWPM();
      speedCounter.textContent = `${Math.round(this.wpn)} WPM`;
    }, 5000);
  },

  methods: {
    average(arr) {
      return arr.reduce((a, b) => a + b, 0) / arr.length;
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
/* Reset */
:root {
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
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
    Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  line-height: 1.6;
  font-size: 1.6rem;
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
  background-color: #f0f0f0;
}

.speed-counter {
  font-size: 5rem;
  font-weight: 500;
  color: #333;
}
</style>
