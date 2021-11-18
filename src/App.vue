<template>
  <div id="app">
    <form id="form" method="post" enctype="multipart/form-data">
      <div class="input-file">
        <label for="file">{{fileName || "Choose file"}}</label>
        <input @change="changeFile" ref="fileForm" class="input-file-display" type="file" id="file" name="file">
        <small class="error-message" v-if="error">{{error}}</small>
      </div>
      <div>
        <button type="button" class="submit-file" @click="submit">Submit</button>
      </div>
      <div class="download-container" v-if="link">
        <a :href="link">Click here to download zip</a>
        <div>
          <h2>File information</h2>
          <p>File name: {{information.fileName}}</p>
          <p>File size: {{information.fileSize}} bytes</p>
          <p>File extension: {{information.fileExtension}}</p>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  data: function() {
    return {
      file: null,
      fileSize: null,
      fileExtension: null,
      fileName: null,
      link: null,
      information: {},
      error: null
    }
  },
  methods: {
    changeFile() {
      this.error = ''
      this.file = this.$refs.fileForm.files[0];
      this.fileName = this.file.name;
      if(this.file.size > 2097152) this.error = 'Your file is bigger than 2MB'
    },
    async submit() {
      try {
        if (!this.file) this.error = 'Please choose file'
        if (this.error) return;
        let formData = new FormData();
        formData.append('file', this.file);
        let response = await axios.post( 'http://localhost:3000/files/upload',
            formData,
            {
              headers: {
                'Content-Type': 'multipart/form-data'
              }
            }
        )
        this.link = response.data.link;
        this.information = response.data.fileInfo;
        this.fileName = ''
        this.file = null;
      } catch(e) {
        alert(`Error: ${e.response.data}`)
      }
    },
  }
}
</script>

<style scoped>
#app {
  display: flex;
  flex-direction: column;
  align-items: center;
}
#form {
  min-width: 440px;
  min-height: 200px;
  background:linear-gradient(180deg, #95BDE0 0%, rgba(209, 193, 221, 0) 64.06%, rgba(151, 75, 210, 0.1) 83.85%);
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 10px;
}

label {
  cursor: pointer;
}
.input-file {
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-width: 180px;
  padding: 5px;
  min-height: 40px;
  background-color: white;
  border-radius: 20px;
  margin-bottom: 10px;
}

.input-file-display {
  display: none;
}

.submit-file {
  border: none;
  border-radius: 10px;
  cursor: pointer;
  width: 130px;
  height: 50px;
  background-color: white;
}

.error-message {
  color: #DB4F4F;
}

.download-container {
  margin-top: 10px;
}

</style>
