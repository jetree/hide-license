<template>
  <div class="container">
    <section>
      <Main />
    </section>
    <section>
      <div id="previewarea"></div>
    </section>
  </div>
</template>

<script>
import Main from '../components/main.vue'

export default {
  components: {
    Main,
  },
}
</script>

<style scoped>
.container {
  width: 100%;
  max-width: 960px;
  margin: 0 auto;
  min-height: 100vh;
  justify-content: center;
  align-items: center;
  text-align: center;
  background-color: azure;
}

.links {
  padding-top: 15px;
}

.preview-area {
  list-style: none;
}
</style>
