<template>
  <div>
    <!-- button -->
    <button
      v-if="!imageURL"
      class="button"
      @click="proxyClick"
    >
      Take Picture
    </button>

    <!-- preview -->
    <img
      v-if="imageURL"
      class="image"
      :src="imageURL"
    >

    <!-- file input -->
    <input
      v-show="false"
      ref="input"
      type="file"
      accept="image/*"
      capture="environment"
      @input="showPreview"
    >
  </div>
</template>

<script>
export default {
  name: 'CaptureImage',

  data() {
    return {
      imageURL: null,
    };
  },

  methods: {
    proxyClick() {
      this.$refs.input.click();
    },
    showPreview(e) {
      if (!e.target.files[0]) return;

      this.imageURL = URL.createObjectURL(e.target.files[0]);
    },
  },
};
</script>

<style scoped>
.button {
  width: 200px;
  margin-top: 60px;
  padding: 10px;
  font-weight: bold;
  color: #ffffff;
  background-color: limegreen;
  border: none;
  border-radius: 4px;
  text-align: center;
  cursor: pointer;
}

.image {
  min-width: 50vw;
  max-width: 100vw;
  min-height: 50vh;
  max-height: 100vh;
}
</style>
