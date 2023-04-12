<template>
  <div class="scanner-container">
    <div v-show="!isLoading">
      <video poster="data:image/gif,AAAA" ref="scanner" width="100%"  height="100%"></video>
      <div class="laser"></div>
    </div>
  </div>
</template>

<script>
import { BrowserMultiFormatReader, Exception } from "@zxing/library";

export default {
  name: "stream-barcode-reader",

  data() {
    return {
      isLoading: true,
      codeReader: new BrowserMultiFormatReader(),
      isMediaStreamAPISupported: navigator && navigator.mediaDevices && "enumerateDevices" in navigator.mediaDevices,
    };
  },

  mounted() {
    if (!this.isMediaStreamAPISupported) {
      throw new Exception("Media Stream API is not supported");
      return;
    }

    this.start();
    this.$refs.scanner.oncanplay = (event) => {
      this.isLoading = false;
      this.$emit("loaded");
    };
  },

  beforeUnmount() {
    this.codeReader.reset();
  },

  methods: {
    start() {
      this.codeReader.decodeFromVideoDevice(undefined, this.$refs.scanner, (result, err) => {
        if (result) {
          this.$emit("decode", result.text);
          this.$emit("result", result);
        }
      });
    },
  },
};
</script>

<style scoped>
video {
  max-width: 90%;
  max-height: 90%;
  object-fit: cover;
  margin: 0 auto;
  overflow: hidden;
}
.scanner-container {
  position: relative;
  border-radius: 10px;
}

.laser {
  width: 90%;
  margin-left: 5%;
  background-color: rgb(43, 160, 176);
  height: 2px;
  position: absolute;
  top: 10%;
  z-index: 2;
  box-shadow: 0 0 4px #00b7ff;
  -webkit-animation: scanning 2s infinite;
  animation: scanning 2s infinite;
}
@-webkit-keyframes scanning {
  50% {
    -webkit-transform: translateY(195px);
    transform: translateY(195px);
  }
}
@keyframes scanning {
  50% {
    -webkit-transform: translateY(250px);
    transform: translateY(250px);
  }
}
</style>