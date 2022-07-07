<template>
  <div class="drop-zone">
    <input
      type="file"
      id="assetsFieldHandle"
      ref="file"
      accept=".csv"
      :class="dropClass"
      @change="uploadFile"
      @dragover="dragover"
      @dragleave="dragleave"
    />
    <label for="file">Click me or drag a CSV file</label>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'DropZone',
  delimiters: ['${', '}'],
  data() {
    return { 
      filelist: [],
      isDropping: false,
    }
  },
  computed: {
    dropClass() {
      if (this.isDropping) {
        return 'drop-zone-input dropping'
      } else {
        return 'drop-zone-input'
      }
    }
  },
  methods: {
    uploadFile(){
      const file = this.$refs.file.files[0];
      if (file.type !== "text/csv") {
        alert('Please upload a CSV file')
      } else{
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = function(){
          const base64String = reader.result
            .replace('data:', '')
            .replace(/^.+,/, '')
          axios.post('http://localhost:8080/api/csv', {
            base64File: base64String
          })
          .then(function (response) {
            const data = response.data;
            console.log(data);
          })
        }
      }
    },
    handleFile(){},
    postFile(){},
    dragover() {
      this.isDropping = true;
    },
    dragleave() {
      this.isDropping = false;
    }
  }
}
</script>

<style scoped>
::-webkit-file-upload-button {
  background: #0000ff00;
  color: #0000ff00;
  border-color: #0000ff00;
}
.drop-zone {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.drop-zone-input {
  color: rgba(0, 0, 0, 0);
  width: 50%;
  height: 50%;
  background-color: #0000ff14;
  padding: 10rem;
  border-radius: 15px;
  cursor: pointer;
  z-index: 2;
}
.dropping{
  background-color: #00ff8014;
}
label{
  cursor: pointer;
  margin-top: -11rem;
  z-index: 1;
  font-family: 'Courier New', Courier, monospace;
  font-size: 2rem;
  font-weight: bold;
}
</style>
