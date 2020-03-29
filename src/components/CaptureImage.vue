<template>
  <div>
    <!-- warnings -->
    <div
      v-if="featureSupportWarn"
      class="warnings"
    >
      <strong>NOTE: </strong>
      This browser does not support HTML attribute capture!
      Please switch to a modern browser for auto camera selection.
    </div>

    <!-- button -->
    <button
      class="button"
      @click="proxyClick"
      :disabled="isUploading"
    >
      Take Picture
    </button>

    <!-- upload state -->
    <div
      v-if="isUploading"
      class="upload-state"
    >
      {{ uploadState }}
    </div>

    <!-- preview -->
    <div>
      <img
        v-if="imageURL"
        class="image"
        :src="imageURL"
      >
    </div>

    <!-- file input -->
    <input
      v-show="false"
      ref="input"
      type="file"
      accept="image/*"
      capture="environment"
      @change="showPreviewAndPostToEndpoint"
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
      endpointURL: 'https://happydelivery.io/webapi/V1/rcvpht.php',
      // upload in progress
      isUploading: false,
      // upload state
      uploadState: '',
      // feature support warning
      featureSupportWarn: false,
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
      // set upload state
      this.uploadState = 'Uploading...';
      this.isUploading = true;

      // POST formData
      return fetch(this.endpointURL, {
        method: 'POST',
        mode: 'cors',
        body: formData,
      });
    },
    hideUploadState(duration) {
      // wait duration seconds and clear upload state
      setTimeout(() => {
        this.isUploading = false;
      }, duration * 1000);
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
        .then((res) => {
          console.log('Upload successful!');
          this.uploadState = `Status: ${res.status}`;
          // wait duration seconds and clear upload state
          this.hideUploadState(5);
        })
        // show some useful info, if any error
        .catch((err) => {
          console.error('Error: ', err);
          this.uploadState = `Error: ${err.message}`;
          // wait duration seconds and clear upload state
          this.hideUploadState(5);
        });
    },
  },

  mounted() {
    // check if browser supports attribute "capture"
    const nosupport = this.$refs.input.attributes.capture === undefined;

    if (nosupport) {
      console.warn('This browser does not support HTML attribute capture! Please switch to a modern browser for auto camera selection.');
      this.featureSupportWarn = true;
    }
  },
};
</script>

<style scoped>
.warnings {
  padding: 10px 0;
  color: orange;
  background-color: #ffffff;
  font-size: 14px;
  font-style: italic;
}

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

.button:disabled {
  background-color: lightslategrey;
  color: #333333;
  cursor: default;
}

.upload-state {
  padding: 10px;
  font-size: 20px;
  font-weight: bold;
  color: salmon;
}

.image {
  max-width: 50vw;
  max-height: 50vh;
}
</style>
