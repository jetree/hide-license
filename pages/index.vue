<template>
  <div class="body">
    <div class="container">
      <section>
        <Main />
      </section>
      <section>
        <div id="previewarea"></div>
      </section>
    </div>
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
.body {
  width: 100%;
  background-color: azure;
}
.container {
  width: 100%;
  max-width: 960px;
  margin: 0 auto;
  min-height: 100vh;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.links {
  padding-top: 15px;
}

#previewarea {
  list-style: none;
  overflow: auto;
}
</style>
