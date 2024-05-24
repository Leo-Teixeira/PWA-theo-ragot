<!-- src/components/Dialer.vue -->
<template>
  <div class="dialer">
    <header>
      <Headder/>
    </header>
    <main>
      <div id="mapid"></div>
    </main>
  </div>
</template>

<script>
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';

export default {
  data() {
    return {
      location: null,

    };
  },
  mounted() {
    this.fetchLocation();
		var mymap = L.map('map').setView([51.505, -0.09], 13);

		L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
			attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
		}).addTo(mymap);

		// Ajouter un marqueur à une position spécifique
		L.marker(this.location).addTo(mymap)
			.bindPopup('Un marqueur personnalisé !')
			.openPopup();

	},
	methods: {
		fetchLocation() {
      if ('geolocation' in navigator) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.location = {
              latitude: position.coords.latitude,
              longitude: position.coords.longitude
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
