<template>
  <input @change="onChangeInput" type="file" id="upload" ref="uploadFile" name="image" accept="image/*" capture="environment" hidden />
</template>
  
  <script>
  import { BrowserMultiFormatReader } from "@zxing/library";
  
  export default {
    name: "image-barcode-reader",
  
    data() {
      return {
        codeReader: new BrowserMultiFormatReader(),
      };
    },
  
    methods: {
      onChangeInput(e) {
        const files = e.target.files || e.dataTransfer.files;
        if (!files.length) return;
  
        const reader = new FileReader();
        reader.onload = this.processFile;
        reader.readAsDataURL(files[0]);
        console.log('uploading')
      },
  
      processFile(e) {
        this.$el.innerHTML += `<img id="image" src="${e.target.result}"/>`;
        const img = document.getElementById('image');
        img.videoWidth = 0;
        this.codeReader.decodeFromImage(img).then((result) => {
          this.$emit("decode", result.text);
          this.$emit("result", result);
        }).catch((err) => {
          this.$emit("error", err);
        })
      },
    },
  };
  </script>