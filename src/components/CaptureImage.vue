<template>
  <div>
    <!-- button -->
    <button
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
      @input="showPreviewAndPostToEndpoint"
    >
  </div>
</template>

<script>
export default {
  name: 'CaptureImage',

  data() {
    return {
      // temporary image blob for preview
      imageURL: null,
      // url to POST image to
      endpointURL: 'https://happydelivery.io',
    };
  },

  methods: {
    proxyClick() {
      this.$refs.input.click();
    },
    getBase64(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.readAsDataURL(file);

        // conversion successful
        reader.onload = () => resolve(reader.result);

        // conversion failed
        reader.onerror = (err) => reject(err);
      });
    },
    postToEndpoint(base64EncodedString) {
      // create payload
      const formData = new FormData();
      formData.append('image', base64EncodedString);

      // show some useful info, start uploading
      console.log(`Uploading image as base64 encoded string to ${this.endpointURL}`);

      // POST formData
      return fetch(this.endpointURL, {
        method: 'POST',
        mode: 'cors',
        body: formData,
      });
    },
    showPreviewAndPostToEndpoint(e) {
      // get image file
      const [file] = e.target.files;

      // show some useful info, if no image
      if (!file) {
        console.error('You did not select any image!');
        return;
      }

      // release memory occupied by previous image file, if any
      if (this.imageURL) {
        URL.revokeObjectURL(this.imageURL);
      }

      // preview image
      this.imageURL = URL.createObjectURL(file);

      // convert image to base64 string
      this.getBase64(file)
        // post base64 encoded string to endpoint
        .then(this.postToEndpoint)
        // show some useful info, success
        .then(() => {
          console.log('Upload successful!');
        })
        // show some useful info, if any error
        .catch((err) => {
          console.error('Error: ', err);
        });
    },
  },
};
</script>

<style scoped>
.button {
  width: 200px;
  margin-top: 30px;
  margin-bottom: 30px;
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
