<template>
  <form @submit.prevent="sendFile" enctype="multipart/form-data">
    <div
      v-if="message"
      :class="`message ${error ? 'is-danger' : 'is-success'}`"
    >
      <div class="message-body">{{ message }}</div>
    </div>
    <div class="field">
      <div class="dropzone">
        <input
          multiple
          type="file"
          name="file"
          @change="selectFile"
          ref="files"
          class="file-input"
        />

        <div v-if="!uploading" class="call-to-action">
          <span>Click to choose or drag your files here</span>
        </div>
        <div v-if="uploading" class="progress-bar"></div>
      </div>

      <div class="content">
        <ul>
          <li v-for="file in uploadedFiles" :key="file.originalname">
            {{ file.originalname }}
          </li>
        </ul>
      </div>
    </div>

    <div class="field">
      <div
        v-for="(file, index) in files"
        :key="index"
        :class="`level ${file.invalidMessage && 'has-text-danger'}`"
      >
        <div class="level-left">
          <div class="level-item">
            {{ file.name }}
            <span v-if="file.invalidMessage"
              >&nbsp; - {{ file.invalidMessage }}</span
            >
          </div>
        </div>
        <div class="level-right">
          <div class="level-item">
            <a href="#" class="delete" @click.prevent="deleteFiles(index)"></a>
          </div>
        </div>
      </div>
    </div>

    <div class="field">
      <button class="button is-info">Send</button>
    </div>
  </form>
</template>

<script>
import axios from "axios";
import _ from "lodash";
export default {
  name: "MultipleUpload",

  data() {
    return {
      files: [],
      uploadFiles: [],
      message: "",
      error: false,
      uploading: false,
      uploadedFiles: [],
      progress: 0
    };
  },

  methods: {
    selectFile() {
      const files = this.$refs.files.files;
      this.uploadFiles = [...this.uploadFiles, ...files];

      this.files = [
        ...this.files,
        ..._.map(files, file => ({
          name: file.name,
          size: file.size,
          type: file.type,
          invalidMessage: this.validate(file)
        }))
      ];
    },
    validate(file) {
      const MAX_SIZE = 200000;
      const allowedTypes = ["image/jpeg", "image/png", "image/gif"];

      if (!allowedTypes.includes(file.type)) {
        return "Not an image";
      }

      if (file.size > MAX_SIZE) {
        return `Max size is ${MAX_SIZE / 1000} Kb`;
      }

      return "";
    },

    deleteFiles(index) {
      this.files.splice(index, 1);
      this.uploadFiles.splice(index, 1);
    },

    async sendFile() {
      const formData = new FormData();
      _.forEach(this.uploadFiles, file => {
        if (this.validate(file) === "") {
          formData.append("files", file);
        }
      });

      try {
        const res = await axios.post("/upload", formData);
        res.data.files.map(item => {
          this.uploadedFiles.push(item);
        });

        this.message = "Files has been uploaded";
        this.files = [];
        this.uploadFiles = [];
      } catch (error) {
        this.message = error.response.data.error;
        this.error = true;
      }
    }
  }
};
</script>
<style scoped>
.dropzone {
  min-height: 200px;
  padding: 10px;
  position: relative;
  cursor: pointer;
  outline: 2px dashed grey;
  outline-offset: -10px;
  background: lightcyan;
  color: dimgray;
}

.input-file {
  opacity: 0;
  width: 100%;
  height: 200px;
  position: absolute;
  cursor: pointer;
}

.dropzone:hover {
  background-color: lightblue;
}

.dropzone .call-to-action {
  font-size: 1.1rem;
  text-align: center;
  padding-top: 70px;
}
</style>
