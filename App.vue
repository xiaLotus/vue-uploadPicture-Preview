<template>
  <div>
    <!-- 文件放置，當上傳時會觸發 previewImage -->
    
    <input type="file" @change="previewImage" ref="fileInput"/>
    <br />
    <!-- 保存並觸發 -->
    <br />
    <button @click="saveImage">保存圖片</button>
    <!-- 刪除預覽 -->
    <br />
    <button @click="deletePreviewImage">刪除預覽圖片</button>
    <!-- 預覽圖片的 Modal -->
    <br />
    <div v-if="showModal" class="modal">
      <span class="close" @click="closePreview">&times;</span>
      <img :src="imageUrl" class="modal-content" />
    </div>
    <!-- src 屬性綁定到 imageUrl 裡面 -->
    <img :src="imageUrl" class="preview-image" @click="openPreview" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 用於預覽圖片的 URL
      imageUrl: '',
      // 用於保存選擇的圖片文件
      selectedImage: null,
      // 控制 Modal 顯示/隱藏
      showModal: false,
    };
  },
  methods: {
    // 預覽
    previewImage(event) {
      // 當文件選擇發生變化時觸發
      const file = event.target.files[0];
      if (file) {
        // 如果之前有讀取器，先取消它
        if (this.reader) {
          this.reader.abort();
        }

        // 創建新的 FileReader
        this.reader = new FileReader();
        
        // 設定讀取完成的回調
        this.reader.onload = (e) => {
          this.imageUrl = e.target.result;
          this.selectedImage = file;
        };

        // 設定讀取失敗的回調
        this.reader.onerror = (error) => {
          console.error('FileReader error:', error);
        };

        // 開始讀取文件
        this.reader.readAsDataURL(file);
      }
    },
    // 保存圖片
    saveImage() {
      if (this.selectedImage) {
        // 創建一個 Blob 對象，表示選擇的圖片文件
        const blob = new Blob([this.selectedImage], { type: this.selectedImage.type });

        // 創建一個 a 標籤，模擬點擊下載
        const link = document.createElement('a');
        link.href = window.URL.createObjectURL(blob);
        link.download = 'saved_image.jpg';
        link.click();
      } else {
        alert('請先選擇圖片');
      }
    },
    // 刪除預覽
    deletePreviewImage() {
      this.imageUrl = '';
      this.selectedImage = null;

      const input = this.$refs.fileInput
      if (input){
        input.value = '';
      }
    },
    // 開啟預覽 Modal
    openPreview() {
      console.log('Opening preview');
      this.$nextTick(() => {
        this.showModal = true;
      })
    },
    // 關閉預覽 Modal
    closePreview() {
      this.showModal = false;
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: relative;
}

.preview-image {
  max-width: 200px;
  cursor: pointer;
  margin-top: 20px; 
}

/* Add styles for the modal */
.modal {
  display: none;
  position: fixed;
  z-index: 1;
  padding-top: 100px;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
}

.modal-content {
  margin: auto;
  display: block;
  max-width: 80%;
  max-height: 80%;
}

.close {
  color: white;
  position: absolute;
  top: 10px;
  right: 25px;
  font-size: 35px;
  font-weight: bold;
  cursor: pointer;
}
</style>
