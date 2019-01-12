<template>
  <div class="about">
    <input type="file" value="选择图片" @change="selectFile">
    <p class="pre">{{imgSrc}}</p>
    <img v-if="imgSrc" :src="imgSrc" alt="预览">
  </div>
</template>

<script>
export default {
  name: 'About',
  data () {
    return {
      imgSrc: ''
    }
  },
  methods: {
    selectFile (e) {
      const reader = new FileReader()
      reader.readAsDataURL(e.target.files[0])
      reader.onload = e => {
        this.imgSrc = e.target.result
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.about {
  .pre {
    word-break: break-all;
  }
}
</style>
