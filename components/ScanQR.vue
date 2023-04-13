<template>
  <div>
    <div class="max-w-[576px] relative mx-auto h-screen bg-slate-900  px-5 py-10">
      <StartApp />
      <section class="mt-20 p-8 h-fit rounded-xl">
        <StreamBarcodeReader @result="onDecode" ref="camera" />
      </section>
      <section class="flex gap-4 mx-auto w-fit">
        <div class="flex flex-col gap-1 w-[77px] items-center justify-center cursor-pointer">
          <div class="text-slate-300 w-fit p-2 rounded-full" :class="isFlashOn ? 'bg-[#00b7ff]' : 'bg-slate-700'" @click="handleFlash">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
              <path stroke-linecap="round" stroke-linejoin="round" d="M12 18v-5.25m0 0a6.01 6.01 0 001.5-.189m-1.5.189a6.01 6.01 0 01-1.5-.189m3.75 7.478a12.06 12.06 0 01-4.5 0m3.75 2.383a14.406 14.406 0 01-3 0M14.25 18v-.192c0-.983.658-1.823 1.508-2.316a7.5 7.5 0 10-7.517 0c.85.493 1.509 1.333 1.509 2.316V18" />
            </svg>
          </div>
          <p class="text-slate-200">Flash</p>
        </div>
        <div class="flex flex-col gap-1 w-[78px] items-center justify-center cursor-pointer">
          <div class="text-slate-300 bg-slate-700 w-fit p-2 rounded-full hover:bg-[#00b7ff]">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
              <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 4.875c0-.621.504-1.125 1.125-1.125h4.5c.621 0 1.125.504 1.125 1.125v4.5c0 .621-.504 1.125-1.125 1.125h-4.5A1.125 1.125 0 013.75 9.375v-4.5zM3.75 14.625c0-.621.504-1.125 1.125-1.125h4.5c.621 0 1.125.504 1.125 1.125v4.5c0 .621-.504 1.125-1.125 1.125h-4.5a1.125 1.125 0 01-1.125-1.125v-4.5zM13.5 4.875c0-.621.504-1.125 1.125-1.125h4.5c.621 0 1.125.504 1.125 1.125v4.5c0 .621-.504 1.125-1.125 1.125h-4.5A1.125 1.125 0 0113.5 9.375v-4.5z" />
              <path stroke-linecap="round" stroke-linejoin="round" d="M6.75 6.75h.75v.75h-.75v-.75zM6.75 16.5h.75v.75h-.75v-.75zM16.5 6.75h.75v.75h-.75v-.75zM13.5 13.5h.75v.75h-.75v-.75zM13.5 19.5h.75v.75h-.75v-.75zM19.5 13.5h.75v.75h-.75v-.75zM19.5 19.5h.75v.75h-.75v-.75zM16.5 16.5h.75v.75h-.75v-.75z" />
            </svg>
          </div>
          <p class="text-slate-200 whitespace-nowrap">Upload QR</p>
        </div>
      </section>
      <div v-show="isOpen">
        <div @click="close" class="absolute backdrop-blur-md bg-slate-500 w-[576px] h-screen z-40 top-0 left-0 opacity-70">
        </div>
        <div class="absolute top-[50%] left-[50%] translate-x-[-50%] translate-y-[-50%] w-fit h-fit z-50 rounded-xl shadow-lg flex items-center justify-center">
          <div v-if="isValidURL" class="bg-white px-2 py-1 rounded-full flex gap-1 w-[280px]">
            <div>
              <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6 border-r border-slate-700 pr-1">
                <path stroke-linecap="round" stroke-linejoin="round" d="M12 21a9.004 9.004 0 008.716-6.747M12 21a9.004 9.004 0 01-8.716-6.747M12 21c2.485 0 4.5-4.03 4.5-9S14.485 3 12 3m0 18c-2.485 0-4.5-4.03-4.5-9S9.515 3 12 3m0 0a8.997 8.997 0 017.843 4.582M12 3a8.997 8.997 0 00-7.843 4.582m15.686 0A11.953 11.953 0 0112 10.5c-2.998 0-5.74-1.1-7.843-2.918m15.686 0A8.959 8.959 0 0121 12c0 .778-.099 1.533-.284 2.253m0 0A17.919 17.919 0 0112 16.5c-3.162 0-6.133-.815-8.716-2.247m0 0A9.015 9.015 0 013 12c0-1.605.42-3.113 1.157-4.418" />
              </svg>
            </div>
            <a :href="result" class="break-words truncate">{{result}}</a>
          </div>
          <div v-else class="w-full bg-white h-auto p-5 rounded-md break-all relative">
            <span>
              {{ result }}
            </span>
            <div @click="copyResult" ref="copyButton" :data-clipboard-text="result" class="absolute top-0 right-0 cursor-pointer">
              <svg v-if="isCopied" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6 text-green-600">
                <path stroke-linecap="round" stroke-linejoin="round" d="M10.125 2.25h-4.5c-.621 0-1.125.504-1.125 1.125v17.25c0 .621.504 1.125 1.125 1.125h12.75c.621 0 1.125-.504 1.125-1.125v-9M10.125 2.25h.375a9 9 0 019 9v.375M10.125 2.25A3.375 3.375 0 0113.5 5.625v1.5c0 .621.504 1.125 1.125 1.125h1.5a3.375 3.375 0 013.375 3.375M9 15l2.25 2.25L15 12" />
              </svg>
              <svg v-else xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                <path stroke-linecap="round" stroke-linejoin="round" d="M15.75 17.25v3.375c0 .621-.504 1.125-1.125 1.125h-9.75a1.125 1.125 0 01-1.125-1.125V7.875c0-.621.504-1.125 1.125-1.125H6.75a9.06 9.06 0 011.5.124m7.5 10.376h3.375c.621 0 1.125-.504 1.125-1.125V11.25c0-4.46-3.243-8.161-7.5-8.876a9.06 9.06 0 00-1.5-.124H9.375c-.621 0-1.125.504-1.125 1.125v3.5m7.5 10.375H9.375a1.125 1.125 0 01-1.125-1.125v-9.25m12 6.625v-1.875a3.375 3.375 0 00-3.375-3.375h-1.5a1.125 1.125 0 01-1.125-1.125v-1.5a3.375 3.375 0 00-3.375-3.375H9.75" />
              </svg>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import Clipboard from 'clipboard';
export default {
    name: "ScanQR",
    data() {
        return {
            result: null,
            isOpen: false,
            link: '',
            isCopied: false,
            isFlashOn: false,
        };
    },
    methods: {
        onDecode(result) {
            this.result = result.text;
            if(!!this.result){
              this.isOpen = true;
            }
        },
        close(){
          this.isOpen = false;
          this.result = null;
        },
        copyResult(){
          this.isCopied = true
        },
        handleFlash(){
          const camera = this.$refs.camera;
          if(this.isFlashOn){
            camera.turnOffFlashlight()
            this.isFlashOn = false
          }else{
            camera.turnOnFlashlight()
            this.isFlashOn = true
          }
        }
    },
    computed: {
      isValidURL(){
        return typeof this.result === 'string' && this.result.match(/(http(s)?:\/\/.)?(www\.)?[-a-zA-Z0-9@:%._\+~#=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#?&//=]*)/g) !== null;
      }
    },
    mounted(){
      // Get the button element by ref
      const button = this.$refs.copyButton;
      
      // Create a new Clipboard instance attached to the button element
      const clipboard = new Clipboard(button);
      
      // Log the text that has been copied to the clipboard
      clipboard.on('success', (e) => {
        console.log('Copied:', e.text);
      });
    }
}
</script>
<style>
body{
  -webkit-transition: all .1s ease-out;
  transition: all .1s ease-out;
  outline: none;
  -webkit-tap-highlight-color: transparent;
}
</style>
