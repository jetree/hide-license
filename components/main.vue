<template>
  <section>
    <div
      :class="{ onArea: onArea }"
      class="drop-area"
      @dragover.prevent="dragOver"
      @drop.prevent="dropFile"
      @dragleave.prevent="dragLeave"
    >
      <span class="square-content" />
      <label>
        <span class="droparea-label">
          画像をドラッグ&ドロップするか、クリックして画像を選択してください
        </span>
        <input
          id="fileInput"
          class="form-control"
          name="img"
          type="file"
          accept=".png, .jpg, .jpeg"
          multiple
          @change="changeFile"
        />
      </label>
    </div>
    <button @click="quickstart()">変換する</button>
  </section>
</template>

<script>
// const GOOGLE_API_KEY = process.env['VISION_API_KEY']

export default {
  data: () => {
    return {
      onArea: false,
      imageData: [],
      imagefiles: [],
      result: [],
    }
  },
  methods: {
    dragOver() {
      this.onArea = true
    },
    dropFile(e) {
      this.changeFile(e)
    },
    changeFile(e) {
      const files = e.target.files || e.dataTransfer.files
      console.log(files)
      // this.$store.dispatch('store/drawPreview', e)
      this.imagePreview(e)
      this.onArea = false
    },
    dragLeave() {
      this.onArea = false
    },
    imagePreview(e) {
      const filedata = e.target.files || e.dataTransfer.files
      console.log('発火')
      for (let i = 0; i < filedata.length; i++) {
        const previewarea = document.getElementById('previewarea')
        let board = document.createElement('canvas')
        let ctx = board.getContext('2d')
        previewarea.appendChild(board)

        let reader = new FileReader()
        reader.onload = (e) => {
          const data = e.target.result || e.dataTransfer.result
          let image = new Image()
          image.onload = () => {
            board.width = image.naturalWidth
            board.height = image.naturalHeight
            ctx.drawImage(image, 0, 0)
          }
          this.imageData.push(data)
          image.src = data
        }
        reader.readAsDataURL(filedata[i])
      }
    },
  },
}
</script>

<style>
.drop-area {
  width: 80%;
  min-width: 200px;
  min-height: 200px;
  margin: 20px auto;
  padding: 10px;
  text-align: center;
  border: 1px dashed #c6c6c6;
  background-color: #f9f9f9;
  position: relative;
}

.form-control {
  position: absolute;
  top: 0;
  left: 0;
  opacity: 0;
  width: 100%;
  height: 100%;
}

.droparea-label {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translateY(-50%) translateX(-50%);
  z-index: 2;
}

.form-control {
  width: 100%;
}

.imagepreview {
  margin: 0 auto;
}
</style>
