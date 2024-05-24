<!-- src/components/Dialer.vue -->
<template>
  <div class="dialer">
    <header>
      <h1>Phone Dialer App</h1>
    </header>
    <main>
      <div class="display">{{ phoneNumber }}</div>
      <div class="keypad">
        <button v-for="key in keys" :key="key" @click="appendNumber(key)">{{ key }}</button>
      </div>
      <button class="call-button" @click="makeCall">Call</button>
    </main>
    <nuxt-link to="/index.vue">
        <button class="home-button">Go to Home</button>
    </nuxt-link>
  </div>
</template>

<script>
export default {
  data() {
    return {
      phoneNumber: '',
      keys: ['1', '2', '3', '4', '5', '6', '7', '8', '9', '*', '0', '#']
    };
  },
  methods: {
    appendNumber(key) {
      this.phoneNumber += key;
    },
    makeCall() {
      if (this.phoneNumber) {
        window.location.href = `tel:${this.phoneNumber}`;
      } else {
        alert('Please enter a phone number.');
      }
    }
  }
};
</script>

<style scoped>
.dialer {
  text-align: center;
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  border: 2px solid #4CAF50;
  border-radius: 10px;
  background-color: #f9f9f9;
}

header {
  background-color: #4CAF50;
  color: white;
  padding: 15px;
  border-radius: 10px 10px 0 0;
}

header h1 {
  margin: 0;
}

.display {
  font-size: 1.5em;
  margin: 20px 0;
  padding: 10px;
  border: 2px solid #ddd;
  border-radius: 5px;
  background-color: white;
}

.keypad {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
  margin-bottom: 20px;
}

.keypad button {
  padding: 20px;
  font-size: 1.2em;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.keypad button:hover {
  background-color: #45a049;
}

.call-button {
  padding: 15px 30px;
  font-size: 1.5em;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.call-button:hover {
  background-color: #45a049;
}
.home-button {
  padding: 15px 30px;
  font-size: 1.5em;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 10px;
}
</style>
