<template>
  <button @click="hideLicense()">変換する{{ imageData }}</button>
</template>

<script>
export default {
  props: {
    imageData: {
      type: Array,
      default: null,
    },
    imageFiles: {
      type: Object,
      default: null,
    },
  },
  methods: {
    hideLicense() {
      // const apiKey = process.env.GOOGLE_API_KEY
      // const url = `https://vision.googleapis.com/v1/images:annotate?key=${apiKey}`;
    },
  },
}
</script>
