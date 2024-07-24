<template>
  <div>
    <h2>Upload de fichier</h2>
    <form @submit.prevent="uploadFile">
      <div>
        <input type="file" @change="handleFileChange" ref="fileInput" accept=".jpg">
      </div>
      <div v-if="selectedFile">
        Fichier sélectionné : {{ selectedFile.name }}
      </div>
      <button type="submit" :disabled="!selectedFile">Envoyer</button>
    </form>
    <div v-if="message" :class="{ 'error': isError, 'success': !isError }">
      {{ message }}
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      selectedFile: null,
      message: '',
      isError: false
    }
  },
  methods: {
    handleFileChange(event) {
      this.selectedFile = event.target.files[0];
    },
    async uploadFile() {
      if (!this.selectedFile) {
        this.message = "Veuillez sélectionner un fichier.";
        this.isError = true;
        return;
      }

      const formData = new FormData();
      formData.append('image', this.selectedFile);

      try {
        this.message = "En cours..."
        await axios.post('http://localhost:5000/submit/image', formData, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }}
        ).then((response) => console.log(response.data))

        this.message = "Fichier uploadé avec succès !";
        this.isError = false;
        this.$refs.fileInput.value = ''; // Réinitialiser l'input file
        this.selectedFile = null;
      } catch (error) {
        this.message = error.response?.data?.message || "Une erreur s'est produite lors de l'upload.";
        this.isError = true;
      }
    }
  }
}
</script>

<style scoped>
.error {
  color: red;
}
.success {
  color: green;
}
</style>