<template>
  <div>
    <header>
      <h1>Photo Capture App</h1>
      <div v-if="location">
        <p>Your location: {{ location.latitude }}, {{ location.longitude }}</p>
      </div>
      <div v-else>
        <p>Fetching location...</p>
      </div>
      <div v-if="battery">
        <p class="battery-status">
          Battery: {{ Math.round(battery.level * 100) }}%
          <span v-if="battery.charging">(Charging)</span>
        </p>
      </div>
      <nuxt-link to="/">
        <button class="home-button">Photo</button>
      </nuxt-link>
      <nuxt-link to="/tel">
        <button class="home-button">Appel</button>
      </nuxt-link>
      <nuxt-link to="/loc">
        <button class="home-button">Wesh t'es où ?</button>
      </nuxt-link>
      <nuxt-link to="/document">
        <button class="home-button">Add document</button>
      </nuxt-link>
    </header>
  </div>
</template>

<script>
export default {
  data() {
    return {
      location: null,
    };
  },
  mounted() {
    this.fetchLocation();
    this.fetchBatteryStatus();
  },
  methods: {
    fetchLocation() {
      if ("geolocation" in navigator) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.location = {
              latitude: position.coords.latitude,
              longitude: position.coords.longitude,
            };
          },
          (error) => {
            console.error("Error fetching location:", error);
          }
        );
      } else {
        console.error("Geolocation is not supported by this browser.");
      }
    },
    async fetchBatteryStatus() {
      if ("getBattery" in navigator) {
        try {
          const battery = await navigator.getBattery();
          this.battery = battery;
          battery.addEventListener("chargingchange", this.updateBatteryStatus);
          battery.addEventListener("levelchange", this.updateBatteryStatus);
        } catch (error) {
          console.error("Error fetching battery status:", error);
        }
      } else {
        console.error("Battery Status API is not supported by this browser.");
      }
    },
  },
};
</script>

<style scoped>
header {
  background-color: #4caf50;
  color: white;
  padding: 15px;
  text-align: center;
  border-radius: 10px;
  margin-bottom: 20px;
  position: relative;
}

header h1 {
  margin: 0;
}

.home-button {
  padding: 15px 30px;
  font-size: 1.5em;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 10px;
}
</style>
