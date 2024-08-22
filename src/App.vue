<template>
  <div class="app">
    <nav class="navbar navbar-dark bg-dark">
      <a class="navbar-brand" href="#">Neverland 資料庫</a>
    </nav>
    <div class="container">
      <div class="wrapper">
        <h1>字數統計器</h1>
        <div class="form-group">
          <label class="col-form-label">輸入角色名：</label>
          <div>
            <input v-model="name" class="form-control" type="text" value="" required>
          </div>
        </div>
        <div class="form-group">
          <el-upload ref="uploadRef" v-model:file-list="fileList" class="upload-demo" drag :auto-upload="false"
            :headers="dropzoneOptions.headers" :data="{ name }" :before-upload="beforeUpload" :http-request="uploadFile"
            multiple>
            {{ dropzoneOptions.dictDefaultMessage }}
          </el-upload>
        </div>
        <div class="action-buttons">
          <button class="btn btn-primary" v-on:click="startCount">
            開始統計
          </button>
        </div>
        <div class="result">
          統計字數： {{ count }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Count',
  data() {
    return {
      name: '',
      count: 0,
      fileList: [],
      uploadedFiles: [],
      dropzoneOptions: {
        url: 'http://127.0.0.1:8000',
        dictDefaultMessage: '將檔案拖至此或點擊上傳',
        acceptedFiles: '.doc, .docx',
        headers: { 'Access-Control-Allow-Origin': '*' }
      }
    };
  },
  methods: {
    async uploadFile(param) {
      if (this.uploadedFiles.length == this.fileList.length){
        const formData = new FormData();
        this.uploadedFiles.forEach(file => {
          formData.append(`${file.name}`, file)
        })
        formData.append('name', this.name);
        const response = await fetch('http://ssh.sardo.work:8000', {
          method: "POST",
          headers: {
            'Access-Control-Allow-Origin': '*',
          },
          body: formData,
        });
        const result = await response.json();
        this.count = result.totalCount;
        this.uploadedFiles = [];
        this.fileList = [];
      }
    },
    beforeUpload(file){
      this.uploadedFiles.push(file);
    },
    startCount() {
      //If name is empty, alert user and will not start upload
      if (this.name === '') {
        alert('請輸入角色姓名！');
      } else {
        this.$refs.uploadRef.submit();
      }
    }
  },
}
</script>

<style scoped>
.navbar {
  margin-bottom: 50px;
}

h1 {
  margin-bottom: 1em;
}

.dropzone {
  margin-bottom: 50px;
}

.uploaded-files {
  margin-top: 1em;
}

.files-list {
  margin-top: 1em;
}

.result {
  font-size: 1.5em;
  margin-top: 50px;
}
</style>
