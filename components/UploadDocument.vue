<template>
  <div>
    <h1>Upload PDF</h1>
    <form @submit.prevent="uploadPDF">
      <input
        type="file"
        @change="onFileChange"
        accept="application/pdf"
        required
      />
      <button type="submit">Upload</button>
    </form>
    <div v-if="text">
      <h2>Extracted Text</h2>
      <pre>{{ text }}</pre>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      file: null,
      text: null,
    };
  },
  methods: {
    onFileChange(event) {
      this.file = event.target.files[0];
    },
    async uploadPDF() {
      const formData = new FormData();
      formData.append("pdf", this.file);

      try {
        const response = await fetch("/upload", {
          method: "POST",
          headers: {
            "Content-Type": "multipart/form-data",
          },
          body: formData,
        });
        if (response.ok) {
          const data = await response.json();
          console.log(data);
          this.text = data.text;
        } else {
          console.log(response);
          this.text = "";
        }
      } catch (error) {
        console.error("Error uploading file:", error);
      }
    },
  },
};
</script>
