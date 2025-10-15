<template>
  <div class="upload-container">
    <div class="upload-wrapper">
      <div class="info-panel">
        <p class="info-panel-title">Finsh! </p>
        <p class="info-panel-title">update Your Resume</p>
        <p class="info-text">
          upload your resume, the platform will help you parse and optimize,you can also skip this step
        </p>
        
        
      </div>
      
      <div class="upload-panel">
        <div class="upload-header">
          <h2>Upload file</h2>
        </div>
        
        <div 
          class="upload-area"
          :class="{ 'dragover': isDragover }"
          @dragover.prevent="onDragOver"
          @dragleave.prevent="onDragLeave"
          @drop.prevent="onDrop"
        >
          <div class="upload-icon">📁</div>
          <p class="upload-hint">
            拖放文件到此处，或点击选择文件<br>
            <small>支持文件格式: PDF, DOC, DOCX, JPG, PNG (最大10MB)</small>
          </p>
          <input 
            type="file" 
            class="file-input"
            @change="onFileSelected"
            ref="fileInput"
          >
        </div>
        
        <div class="file-info" v-if="selectedFile">
          <span class="file-icon">📄</span>
          <div class="file-details">
            <div class="file-name">{{ selectedFile.name }}</div>
            <div class="file-size">{{ formatFileSize(selectedFile.size) }}</div>
          </div>
          <button class="remove-file" @click="removeFile">✕</button>
        </div>
        
        <div class="button-group">
          <button 
            class="btn btn-secondary"
            @click="cancelUpload"
            :disabled="!selectedFile"
          >
            取消
          </button>
          <button 
            class="btn btn-primary"
            @click="uploadFile"
            :disabled="!selectedFile || isUploading"
          >
            {{ isUploading ? '上传中...' : '开始上传' }}
          </button>
        </div>
        
        <div class="success-message" v-if="uploadComplete">
          <div class="success-icon">✅</div>
          <h3>上传成功！</h3>
          <p>您的文件已成功上传至服务器。</p>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'UploadComponent',
  data() {
    return {
      isDragover: false,
      selectedFile: null,
      isUploading: false,
      uploadComplete: false
    }
  },
  methods: {
    onDragOver() {
      this.isDragover = true;
    },
    onDragLeave() {
      this.isDragover = false;
    },
    onDrop(e) {
      this.isDragover = false;
      const files = e.dataTransfer.files;
      if (files.length > 0) {
        this.handleFile(files[0]);
      }
    },
    onFileSelected(e) {
      const files = e.target.files;
      if (files.length > 0) {
        this.handleFile(files[0]);
      }
    },
    handleFile(file) {
      // 验证文件类型
      const allowedTypes = [
        'application/pdf',
        'application/msword',
        'application/vnd.openxmlformats-officedocument.wordprocessingml.document',
        'image/jpeg',
        'image/png'
      ];
      
      if (!allowedTypes.includes(file.type)) {
        alert('不支持的文件类型！请上传PDF、Word文档或图片文件。');
        return;
      }
      
      // 验证文件大小 (10MB)
      const maxSize = 10 * 1024 * 1024; // 10MB
      if (file.size > maxSize) {
        alert('文件大小超过10MB限制！');
        return;
      }
      
      this.selectedFile = file;
      this.uploadComplete = false;
    },
    removeFile() {
      this.selectedFile = null;
      this.$refs.fileInput.value = '';
    },
    cancelUpload() {
      this.removeFile();
    },
    uploadFile() {
      if (!this.selectedFile) return;
      
      this.isUploading = true;
      
      // 模拟上传过程
      setTimeout(() => {
        this.isUploading = false;
        this.uploadComplete = true;
        
        // 3秒后重置状态
        setTimeout(() => {
          this.removeFile();
          this.uploadComplete = false;
        }, 3000);
      }, 2000);
    },
    formatFileSize(bytes) {
      if (bytes === 0) return '0 Bytes';
      const k = 1024;
      const sizes = ['Bytes', 'KB', 'MB', 'GB'];
      const i = Math.floor(Math.log(bytes) / Math.log(k));
      return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i];
    }
  }
}
</script>
<style scoped>
.upload-container {
  max-width: 1000px;
  width: 100%;
  background: rgb(255, 255, 255);
  border-radius: 12px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  overflow: hidden;
}

.upload-wrapper {
  display: flex;
  min-height: 500px;
  background-color: #f2f3f4;
}

.info-panel {
  text-align: left;
  flex: 4;
  color: white;
  padding-left: 40px;
  padding-top: 80px;
  display: flex;
  flex-direction: column;
  

}

.upload-panel {
  margin: 60px;
  flex: 6;
  padding: 40px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  border-radius: 15px;
  background-color: #ffffff;
  box-shadow: 
    -5px 10px 20px 0 rgba(166, 182, 228, 0.2),
    5px -10px 20px 0 rgba(166, 182, 228, 0.2);
}

h1 {
  font-size: 2.2rem;
  margin-bottom: 20px;
  font-weight: 600;
}

.info-text {
  color: #737272; 
  font-size: 0.7rem;
  line-height: 1.2;
  margin-bottom: 30px;
}
.info-panel-title{
  margin: 0;
  font-size: 1.5rem;
  padding: 0px;
  color: #000;
}

.features {
  margin-top: 30px;
}

.feature {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
  font-size: 1rem;
}

.feature-icon {
  margin-right: 12px;
  font-size: 1.5rem;
}

.upload-header {
  text-align: left;
  margin-bottom: 30px;
}

.upload-header h2 {
  font-size: 1.8rem;
  color: #2c3e50;
  margin-bottom: 10px;
}

.upload-header p {
  color: #7f8c8d;
  font-size: 1.1rem;
}

.upload-area {
  border: 2px dashed #3498db;
  border-radius: 8px;
  padding: 40px 20px;
  text-align: center;
  background-color: #f8f9fa;
  transition: all 0.3s ease;
  cursor: pointer;
  margin-bottom: 25px;
  position: relative;
}

.upload-area:hover {
  border-color: #2980b9;
  background-color: #ecf0f1;
}

.upload-area.dragover {
  border-color: #27ae60;
  background-color: #d5f4e6;
}

.upload-icon {
  font-size: 3.5rem;
  color: #3498db;
  margin-bottom: 15px;
}

.upload-hint {
  color: #7f8c8d;
  font-size: 1rem;
  line-height: 1.6;
}

.file-input {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
  cursor: pointer;
  
}

.file-info {
  padding: 15px;
  background: #e8f4fd;
  border-radius: 6px;
  margin-bottom: 25px;
  border-left: 4px solid #3498db;
  display: flex;
  align-items: center;
  gap: 15px;
}

.file-icon {
  font-size: 2rem;
  color: #3498db;
}

.file-details {
  flex: 1;
}

.file-name {
  font-weight: 600;
  color: #2c3e50;
  margin-bottom: 5px;
  word-break: break-all;
}

.file-size {
  font-size: 0.9rem;
  color: #7f8c8d;
}

.remove-file {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  color: #e74c3c;
  transition: transform 0.2s;
}

.remove-file:hover {
  transform: scale(1.1);
}

.button-group {
  display: flex;
  gap: 15px;
  justify-content: center;
}

.btn {
  padding: 12px 30px;
  border: none;
  border-radius: 6px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  min-width: 120px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.btn-primary {
  background: #7ec1ed;
  color: white;
  box-shadow: 0 4px 6px rgba(52, 152, 219, 0.3);
}

.btn-primary:hover {
  background: #2980b9;
  transform: translateY(-2px);
  box-shadow: 0 6px 10px rgba(52, 152, 219, 0.4);
}

.btn-primary:active {
  transform: translateY(0);
  box-shadow: 0 2px 4px rgba(52, 152, 219, 0.3);
}

.btn-secondary {
  background: #7ec1ed;
  color: white;
  box-shadow: 0 4px 6px rgba(52, 152, 219, 0.3);
}

.btn-secondary:hover {
  background: #2980b9;
  transform: translateY(-2px);
}

.btn-secondary:active {
  transform: translateY(0);
  box-shadow: 0 2px 4px rgba(52, 152, 219, 0.3);
}

.btn:disabled {
  background: #afafaf;
  cursor: not-allowed;
  transform: none;
  box-shadow: none;
}

.success-message {
  text-align: center;
  padding: 20px;
  background: #d5f4e6;
  border-radius: 8px;
  margin-top: 20px;
  border-left: 4px solid #27ae60;
}

.success-icon {
  font-size: 2.5rem;
  color: #27ae60;
  margin-bottom: 10px;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .upload-wrapper {
    flex-direction: column;
  }
  
  .info-panel, .upload-panel {
    padding: 30px 20px;
  }
  
  .info-panel {
    order: 2;
  }
  
  .upload-panel {
    order: 1;
  }
  
  .upload-area {
    padding: 30px 15px;
  }
  
  .button-group {
    flex-direction: column;
  }
  
  .btn {
    width: 100%;
  }
}

@media (max-width: 480px) {
  h1 {
    font-size: 1.8rem;
  }
  
  .upload-header h2 {
    font-size: 1.5rem;
  }
}
</style>
