<template>
  <div>
    <div class="max-w-[576px] relative mx-auto h-screen bg-slate-900  px-5 py-10">
      <StartApp />
      <section class="mt-20 p-8 h-fit rounded-xl">
        <StreamBarcodeReader @result="onDecode" />
      </section>
      <p class="text-slate-300 text-center text-sm mt-4">Create by Mengyi</p>
      <p class="text-slate-300 text-center text-sm mt-4">version 0.1</p>
      <div v-if="isOpen">
        <div @click="close" class="absolute bg-slate-500 w-[576px] h-screen z-40 top-0 left-0 opacity-50 backdrop-blur-xl">
        </div>
        <div class="absolute w-[90%] top-[50%] left-[50%] translate-x-[-50%] translate-y-[-50%] bg-white h-[360px] z-50 rounded-xl shadow-lg flex items-center justify-center">
          <a v-if="isLink" :href="link[0]">{{result}}</a>
          <p v-else class="break-words">{{result}}</p>
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
            result: '',
            isOpen: false,
            isLink: false,
            link: '',
        };
    },
    methods: {
        onDecode(result) {
            console.log(result);
            check(result)
            this.result = result;
            if(this.result.length > 0){
              this.isOpen = true;
            }
        },
        close(){
          this.isOpen = false;
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
}
</script>
