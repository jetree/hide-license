<template>
  <div class="container">
    <div class="column area">
      <droparea />
      <button @click="quickstart()">変換する</button>
    </div>
    <previewarea />
    <!-- <div class="column area">
      <div class="preview-area test">
        <canvas id="board"></canvas>
      </div>
    </div> -->
  </div>
</template>

<script>
// import droparea from '../components/droparea.vue'
export default {
  // components: true,
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.column {
  margin: 0 auto;
  width: 80%;
}

.area {
  padding-top: 15px;
  margin: 0 auto;
}

.links {
  padding-top: 15px;
}

.preview-area {
  list-style: none;
}
</style>
