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
      <nuxt-link to="/tel">
        <button class="home-button">Appel</button>
      </nuxt-link>
    </header>
    <main>
      <video ref="video" autoplay></video>
      <button @click="startCamera">Start Camera</button>
      <button @click="stopCamera">Stop Camera</button>
      <button @click="takePhoto">Take Photo</button>
      <canvas ref="canvas" style="display:none;"></canvas>
      <div class="photo-list">
        <div v-for="(photo, index) in photos" :key="index" class="photo-item">
          <img :src="photo.url" alt="Captured photo">
          <p>{{ photo.time }}</p>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      stream: null,
      videoElement: null,
      canvasElement: null,
      photos: [],
      location: null
    };
  },
  mounted() {
    this.videoElement = this.$refs.video;
    this.canvasElement = this.$refs.canvas;
    this.requestNotificationPermission();
    this.fetchLocation();
    this.loadPhotos();
  },
  methods: {
    async startCamera() {
      try {
        this.stream = await navigator.mediaDevices.getUserMedia({ video: true });
        this.videoElement.srcObject = this.stream;
      } catch (error) {
        console.error("Error accessing camera:", error);
      }
    },
    stopCamera() {
      if (this.stream) {
        const tracks = this.stream.getTracks();
        tracks.forEach(track => track.stop());
        this.videoElement.srcObject = null;
      }
    },
    takePhoto() {
      if (!this.stream) {
        return;
      }
      const context = this.canvasElement.getContext('2d');
      this.canvasElement.width = this.videoElement.videoWidth;
      this.canvasElement.height = this.videoElement.videoHeight;
      context.drawImage(this.videoElement, 0, 0, this.canvasElement.width, this.canvasElement.height);
      const photoDataUrl = this.canvasElement.toDataURL('image/png');
      const currentTime = new Date().toLocaleString();
      const photo = { url: photoDataUrl, time: currentTime };
      this.photos.push(photo);
      this.savePhotos();
      this.showNotification('Photo taken', 'Your photo has been captured successfully!');
    },
    requestNotificationPermission() {
      if ('Notification' in window) {
        Notification.requestPermission().then(permission => {
          if (permission !== 'granted') {
            console.warn('Permission not granted for notifications');
          }
        });
      }
    },
    showNotification(title, body) {
      if ('Notification' in window && Notification.permission === 'granted') {
        new Notification(title, { body });
      }
    },
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
    savePhotos() {
      localStorage.setItem('photos', JSON.stringify(this.photos));
    },
    loadPhotos() {
      const photos = localStorage.getItem('photos');
      if (photos) {
        this.photos = JSON.parse(photos);
      }
    }
  }
};
</script>

<style scoped>
header {
  background-color: #4CAF50;
  color: white;
  padding: 15px;
  text-align: center;
  border-radius: 10px;
  margin-bottom: 20px;
}

header h1 {
  margin: 0;
}

main {
  display: flex;
  flex-direction: column;
  align-items: center;
}

button {
  margin: 10px;
  padding: 10px 20px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}

.photo-list {
  margin-top: 20px;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

.photo-item {
  margin: 10px;
  text-align: center;
}

.photo-item img {
  width: 100px;
  height: auto;
  border: 2px solid #ddd;
  border-radius: 5px;
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
