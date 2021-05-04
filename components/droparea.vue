<template>
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
      this.imagefiles.push(files)
      // console.log(this.imagefiles)
      this.onArea = false
      // this.imagePreview(e)
      // this.test(e)
    },
    dragLeave() {
      this.onArea = false
    },
    imagePreview(e) {
      const filedata = e.target.files || e.dataTransfer.files
      for (let i = 0; i < filedata.length; i++) {
        let reader = new FileReader()
        reader.onload = (e) => {
          const data = e.target.result || e.dataTransfer.result
          console.log({ data })
          this.imageData.push(data)
        }
        reader.readAsDataURL(filedata[i])
      }
    },
  },
}
</script>

<style>
.drop-area {
  width: 100%;
  min-width: 200px;
  min-height: 200px;
  padding: 10px;
  text-align: center;
  border: 1px dashed #c6c6c6;
  background-color: #f9f9f9;
  position: relative;
}
//ドロップエリアを正方形に保つため
.square-content {
  display: block;
  height: 0;
  padding-bottom: 100%;
}

.form-control {
  width: 100%;
  border-radius: 5px;
  display: none;
}

.droparea-label {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translateY(-50%) translateX(-50%);
  z-index: 2;
}
</style>
