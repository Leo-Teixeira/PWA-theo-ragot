<template>
  <div>
    <main>
      <video ref="video" autoplay></video>
      <button @click="startCamera">Start Camera</button>
      <button @click="stopCamera">Stop Camera</button>
      <button @click="takePhoto">Take Photo</button>
      <canvas ref="canvas" style="display:none;"></canvas>
      <div class="photo-list">
        <div v-for="(photo, index) in photos" :key="index" class="photo-item">
          <img :src="photo.url" alt="Captured photo" :class="photo.isOnline ? 'online' : 'offline'">
          <p>{{ photo.time }}</p>
          <button @click="deletePhoto(index)">Delete</button>
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
      location: null,
      battery: null,
      isOnline: navigator.onLine,
    };
  },
  beforeUnmount() {
    window.removeEventListener('online', this.updateOnlineStatus);
    window.removeEventListener('offline', this.updateOnlineStatus);
  },
  mounted() {
    this.videoElement = this.$refs.video;
    this.canvasElement = this.$refs.canvas;
    this.requestNotificationPermission();
    this.loadPhotos();
    window.addEventListener('online', this.updateOnlineStatus);
    window.addEventListener('offline', this.updateOnlineStatus);
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
      const dataUrl = this.canvasElement.toDataURL('image/png');
      const photo = {
        url: dataUrl,
        time: new Date().toLocaleString(),
        isOnline: this.isOnline
      };
      this.photos.push(photo);
      this.savePhotos();
      this.showNotification('Photo taken', 'Your photo has been captured successfully!');
    },
    deletePhoto(index) {
      this.photos.splice(index, 1);
      this.savePhotos();
    },
    requestNotificationPermission() {
      if ('Notification' in window) {
        Notification.requestPermission().then(permission => {
          if (permission !== 'granted') {
            console.log('Permission not granted for notifications');
          }
        });
      }
    },
    showNotification(title, body) {
      if ('Notification' in window && Notification.permission === 'granted') {
        new Notification(title, { body });
        this.triggerVibration();
      }
    },
    triggerVibration() {
      if ('vibrate' in navigator) {
        navigator.vibrate([200, 100, 200]);
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
    },
    async updateOnlineStatus() {
      this.isOnline = navigator.onLine;
      if (this.isOnline) {
        // Mettre à jour les photos pour marquer celles qui étaient hors ligne comme maintenant en ligne
        this.photos.forEach(photo => {
          if (!photo.isOnline) {
            photo.isOnline = true;
          }
        });
        localStorage.setItem('photos', JSON.stringify(this.photos));
        this.$store.dispatch('updatePhotos', this.photos);
        await this.notify('Synchronisation des images effectuée !');
        alert('Synchronisation des images effectuée !');
      }
    }
  }
};
</script>

<style scoped>

.battery-status {
  position: absolute;
  top: 15px;
  right: 15px;
  font-size: 0.9em;
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

.online {
  border: 5px solid green;
}

.offline {
  border: 5px solid red;
}
</style>
