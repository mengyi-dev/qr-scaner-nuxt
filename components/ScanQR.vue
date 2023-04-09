<template>
  <div>
    <div class="max-w-[576px] relative mx-auto h-screen bg-slate-900  px-5 py-10">
      <StartApp />
      <section class="mt-20 p-8 h-fit rounded-xl">
        <StreamBarcodeReader @result="onDecode" ref="stopRef" />
      </section>
      <p class="text-slate-300 text-center text-sm mt-4">Created by Mengyi</p>
      <p class="text-slate-300 text-center text-sm mt-4">version 0.1</p>
      <div v-if="isOpen">
        <div @click="close" class="absolute backdrop-blur-md bg-slate-500 w-[576px] h-screen z-40 top-0 left-0 opacity-70">
        </div>
        <div class="absolute top-[50%] left-[50%] translate-x-[-50%] translate-y-[-50%] w-fit h-fit z-50 rounded-xl shadow-lg flex items-center justify-center">
          <!-- <a v-if="isLink" :href="link[0]">{{result}}</a> -->
          <div class="bg-white px-2 py-1 rounded-full flex gap-1 w-[280px]">
            <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6 border-r border-slate-700 pr-1">
              <path stroke-linecap="round" stroke-linejoin="round" d="M12 21a9.004 9.004 0 008.716-6.747M12 21a9.004 9.004 0 01-8.716-6.747M12 21c2.485 0 4.5-4.03 4.5-9S14.485 3 12 3m0 18c-2.485 0-4.5-4.03-4.5-9S9.515 3 12 3m0 0a8.997 8.997 0 017.843 4.582M12 3a8.997 8.997 0 00-7.843 4.582m15.686 0A11.953 11.953 0 0112 10.5c-2.998 0-5.74-1.1-7.843-2.918m15.686 0A8.959 8.959 0 0121 12c0 .778-.099 1.533-.284 2.253m0 0A17.919 17.919 0 0112 16.5c-3.162 0-6.133-.815-8.716-2.247m0 0A9.015 9.015 0 013 12c0-1.605.42-3.113 1.157-4.418" />
            </svg>
  
            <a :href="result" class="break-words truncate">https://tailwindcss.com/docs/text-overflow</a>

          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>

export default {
    name: "ScanQR",
    data() {
        return {
            result: null,
            isOpen: false,
            isLink: false,
            link: '',
        };
    },
    methods: {
        onDecode(result) {
            console.log(result);
            // check(result)
            this.result = result;
            if(!!this.result){
              this.isOpen = true;
              const childComponent = this.$refs.stopRef;
              childComponent.stop();
            }
        },
        close(){
          this.isOpen = false;
          this.result = null;
        },
        check(result){
          const urlRegex = /(https?:\/\/[^\s]+)/;
          if (urlRegex.test(result)) {
            this.isLink = true
            this.link = result.match(/\bhttps?:\/\/\S+/gi)
          } else {
            this.isLink = false
          }
        }
    },
    mounted(){
      // Disables the page zoom
      document.addEventListener('gesturestart', function (e) {
        e.preventDefault();
      });
    }
}
</script>
<style>
html {
  touch-action: manipulation;
  -webkit-text-size-adjust: none;
  zoom: 1.0;
  -moz-text-size-adjust: none;
  -ms-text-size-adjust: none;
  -o-text-size-adjust: none;
}
</style>
