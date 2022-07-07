<template>
  <div class="drop-zone">
    <h2>
      {{title}}
    </h2>
    <h4 class="restart" v-if="this.data" v-on:click="restart">
      Click here to restart
    </h4>
    <input
      v-if="!this.data"
      type="file"
      id="assetsFieldHandle"
      ref="file"
      accept=".csv"
      :class="dropClass"
      @change="uploadFile"
      @dragover="dragover"
      @dragleave="dragleave"
      @drop="dragleave"
    />
    <label v-if="!this.data" for="file">
      Click me or drag a CSV file
    </label>
    <div class="dataFormatted">
      {{ formatterData }}
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'DropZone',
  data() {
    return {
      fileList: [],
      isDropping: false,
      isDropped: false,
      data: null,
    }
  },
  computed: {
    dropClass() {
      if (this.isDropping) {
        return 'drop-zone-input dropping'
      } else if (this.isDropped) {
        return 'drop-zone-input dropped'
      }
      return 'drop-zone-input'
    },
    formatterData(){
      if (!this.data) {
        return ''
      }
      return JSON.stringify(this.data, null, '\t')
    },
    title(){
      if (!this.data) {
        return 'Drop a CSV file here'
      }
      return 'You can find the CSV data as a JSON below'
    }
  },
  methods: {
    uploadFile(){
      const file = this.$refs.file.files[0];
      if (file.type !== "text/csv") {
        alert('Please upload a CSV file')
        return
      }
      this.encodeFile(file);
    },
    encodeFile(file){
      const reader = new FileReader();
      reader.readAsDataURL(file);
      const handleEncodedFile = this.handleEncodedFile;
      reader.onload = function() {
        handleEncodedFile(reader.result);
      }
    },
    handleEncodedFile(encodeFile){
      const base64File = encodeFile
        .replace('data:', '')
        .replace(/^.+,/, '');
      this.postFile(base64File);
    },
    postFile(base64File){
      const axiosPromise = axios.post(
        'http://localhost:8080/api/csv',
        { base64File: base64File }
      )
      axiosPromise.then(response => this.data = response.data)
    },
    dragover() {
      this.isDropping = true;
    },
    dragleave() {
      this.isDropping = false;
    },
    restart(){
      this.data = null;
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
  white-space: pre;
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
  border: 1px dashed #070255;
}
.dropping{
  background-color: #00ff8014;
  border: 1px solid #0466357c;
}
.dropped{
  border-color: green
}
.dataFormatted{
  text-align: start;
  padding: 4rem;
}
.restart{
  cursor: pointer;
  color: #0000ff70;
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