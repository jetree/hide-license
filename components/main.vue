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
          画像をドラッグ&ドロップするか、<br />クリックして画像を選択してください
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
    <HideLicenseButton
      :image-data="imageData"
      :image-files="imageFiles"
      @click="quickstart()"
    >
      変換する
    </HideLicenseButton>
  </section>
</template>

<script>
// import HideLicenseButton from './hide-licenseButton.vue'
// const GOOGLE_API_KEY = process.env['VISION_API_KEY']

export default {
  data: () => {
    return {
      onArea: false,
      imageData: [],
      imageFiles: [],
      result: [],
      imageName: [],
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
      // this.imagePreview(e)
      this.previewFiles(e)
      this.onArea = false
    },
    dragLeave() {
      this.onArea = false
    },
    async previewFiles(e) {
      const filedata = e.target.files || e.dataTransfer.files
      // console.log(filedata)
      this.imageFiles.push(filedata)
      // if (filedata) {
      //   ;[].forEach.call(filedata, this.readAndPreview)
      // }
      for (let file of filedata) {
        // console.log({ file })
        let result = await this.readAndPreview(file)
        this.imageData.push(result)
      }
    },
    async readAndPreview(file) {
      const previewarea = document.getElementById('previewarea')
      let reader = new FileReader()
      let board = document.createElement('canvas')
      let ctx = board.getContext('2d')
      let that = []
      previewarea.appendChild(board)
      reader.onload = (e) => {
        const result = e.target.result || e.dataTransfer.result
        that.push(result)
        console.log({ e })
        let image = new Image()
        image.onload = () => {
          board.width = image.naturalWidth
          board.height = image.naturalHeight
          ctx.drawImage(image, 0, 0)
        }
        // this.imageData.push(result)
        image.src = result
      }
      reader.readAsDataURL(file)
      return that
      // reader.addEventListener(
      //   'load',
      //   function () {
      //     let board = document.createElement('canvas')
      //     let ctx = board.getContext('2d')
      //     previewarea.appendChild(board)
      //     let image = new Image()
      //     board.width = image.naturalWidth
      //     board.height = image.naturalHeight
      //     ctx.drawImage(image, 0, 0)
      //     image.src = this.result
      //     console.log(that.imageData)
      //     that.imageData.push(this.result)
      //     // console.log(this.result)
      //     return this.result
      //   },
      //   false
      // )
      // this.imageData.push(file)
    },
    async imagePreview(e) {
      console.log(e)
      const filedata = e.target.files || e.dataTransfer.files
      this.imageFiles.push(filedata)
      for (let i = 0; i < filedata.length; i++) {
        console.log({ i })
        this.imageName.push(filedata[i].name)
        const previewarea = document.getElementById('previewarea')
        let board = document.createElement('canvas')
        let ctx = board.getContext('2d')
        previewarea.appendChild(board)

        let reader = new FileReader()
        reader.onload = (e) => {
          const result = e.target.result || e.dataTransfer.result
          console.log({ e })
          console.log({ result })
          let image = new Image()
          image.onload = () => {
            board.width = image.naturalWidth
            board.height = image.naturalHeight
            ctx.drawImage(image, 0, 0)
          }
          this.imageData.push(result)
          image.src = result
        }
        reader.readAsDataURL(filedata[i])
      }
    },
  },
}
</script>

<style scoped>
.drop-area {
  width: 80%;
  min-width: 200px;
  min-height: 200px;
  margin: 20px auto;
  padding: 10px;
  text-align: center;
  border: 2px dashed #c6c6c6;
  background-color: #f9f9f9;
  position: relative;
  border-radius: 2rem;
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
