<template>
  <div class="scanner-container">
    <div v-show="!isLoading">
      <video poster="data:image/gif,AAAA" ref="scanner" class="aspect-square">
      </video>
      <div class="laser"></div>
      <div class="corner-border-scan"></div>
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
  max-height: auto;
  object-fit: cover;
  margin: 0 auto;
  overflow: hidden;
  position: relative;
}
.scanner-container {
  position: relative;
  border-radius: 10px;
}
.corner-border-scan {
  width: 92%;
  height: 102.3%;
  position: absolute;
  top: -1.15%;
  margin-left: 4%;
  background:
    linear-gradient(to right, #00b7ff 4px, transparent 4px) 0 0,
    linear-gradient(to right, #00b7ff 4px, transparent 4px) 0 100%,
    linear-gradient(to left, #00b7ff 4px, transparent 4px) 100% 0,
    linear-gradient(to left, #00b7ff 4px, transparent 4px) 100% 100%,
    linear-gradient(to bottom, #00b7ff 4px, transparent 4px) 0 0,
    linear-gradient(to bottom, #00b7ff 4px, transparent 4px) 100% 0,
    linear-gradient(to top, #00b7ff 4px, transparent 4px) 0 100%,
    linear-gradient(to top, #00b7ff 4px, transparent 4px) 100% 100%;
  background-repeat: no-repeat;
  background-size: 20px 20px;
}

.laser {
  width: 92%;
  position: absolute;
  margin-left: 4%;
  background-color: rgb(43, 160, 176);
  height: 2px;
  top: 20%;
  z-index: 2;
  box-shadow: 0 0 4px #00b7ff;
  -webkit-animation: scanning 2s infinite;
  animation: scanning 2s infinite;
}
@-webkit-keyframes scanning {
  50% {
    -webkit-transform: translateY(180px);
    transform: translateY(180px);
  }
}
@keyframes scanning {
  50% {
    -webkit-transform: translateY(180px);
    transform: translateY(180px);
  }
}
</style>