<template>
  <div class="home">
    <div v-if="vertical.default">
      <p>0</p>
      <img :src="vertical.default" alt="0">
    </div>
    <div v-if="vertical.right">
      <p>90</p>
      <img :src="vertical.right" alt="90">
    </div>
    <div v-if="vertical.down">
      <p>180</p>
      <img :src="vertical.down" alt="180">
    </div>
    <div v-if="vertical.left">
      <p>270</p>
      <img :src="vertical.left" alt="270">
    </div>
    <hr />
    <div v-if="horizontal.default">
      <p>0</p>
      <img :src="horizontal.default" alt="0">
    </div>
    <div v-if="horizontal.right">
      <p>90</p>
      <img :src="horizontal.right" alt="90">
    </div>
    <div v-if="horizontal.down">
      <p>180</p>
      <img :src="horizontal.down" alt="180">
    </div>
    <div v-if="horizontal.left">
      <p>270</p>
      <img :src="horizontal.left" alt="270">
    </div>
  </div>
</template>

<script>
import {vertical, horizontal} from '../assets/dome.js'

export default {
  name: 'HelloWorld',
  data () {
    return {
      vertical: {
        default: '',
        right: '',
        down: '',
        left: ''
      },
      horizontal: {
        default: '',
        right: '',
        down: '',
        left: ''
      }
    }
  },
  mounted () {
    this.init()
  },
  methods: {
    async init () {
      const verticalImg = await this.loadIMG(vertical)
      this.vertical.default = await this.verticalRotate(verticalImg, 0)
      this.vertical.right = await this.verticalRotate(verticalImg, 90)
      this.vertical.down = await this.verticalRotate(verticalImg, 180)
      this.vertical.left = await this.verticalRotate(verticalImg, 270)
      const horizontalImg = await this.loadIMG(horizontal)
      this.horizontal.default = await this.horizontalRotate(horizontalImg, 0)
      this.horizontal.right = await this.horizontalRotate(horizontalImg, 90)
      this.horizontal.down = await this.horizontalRotate(horizontalImg, 180)
      this.horizontal.left = await this.horizontalRotate(horizontalImg, 270)
    },
    async horizontalRotate (img = {}, rotate = 0) {
      const width = img.width
      const height = img.height
      const canvas = document.createElement('canvas')
      const ctx = canvas.getContext('2d')
      canvas.width = width
      canvas.height = height

      if (rotate === 90 || rotate === 270) {
        canvas.width = width > height ? width : height
        canvas.height = width > height ? width : height
      }

      ctx.translate(width/2, height/2)
      ctx.rotate(rotate*Math.PI/180)
      ctx.drawImage(img, 0, 0, width, height, -canvas.width/2, -canvas.height/2, width, height)

      if (rotate === 90) {
        ctx.drawImage(img, 0, 0, width, height, -canvas.width/2+(width-height)/2, -canvas.height/2, width, height)
      }

      if (rotate === 270) {
        ctx.drawImage(img, 0, 0, width, height, -canvas.width/2+(height-width)/2, -canvas.height/2, width, height)
      }

      let result = canvas.toDataURL()

      if (rotate === 90) {
        result = await this.cutOverflow90Horizontal(result, width, height)
      }

      if (rotate === 270) {
        result = await this.cutOverflow270Horizontal(result, width, height)
      }

      return result
    },
    async verticalRotate (img = {}, rotate = 0) {
      const width = img.width
      const height = img.height
      const canvas = document.createElement('canvas')
      const ctx = canvas.getContext('2d')
      canvas.width = width
      canvas.height = height

      if (rotate === 90 || rotate === 270) {
        canvas.width = width > height ? width : height
        canvas.height = width > height ? width : height
      }

      ctx.translate(width/2, height/2)
      ctx.rotate(rotate*Math.PI/180)

      let canvasStartX = -canvas.width/2;
      let canvasStartY = -canvas.height/2;

      ctx.drawImage(img, 0, 0, width, height, canvasStartX, canvasStartY, width, height)

      if (rotate === 90) {
        ctx.drawImage(img, 0, 0, width, height, -canvas.width/2, -canvas.height/2+(width-height)/2, width, height)
      }

      if (rotate === 270) {
        ctx.drawImage(img, 0, 0, width, height, -canvas.width/2, -canvas.height/2+(height-width)/2, width, height)
      }

      let result = canvas.toDataURL()

      if (rotate === 90) {
        result = await this.cutOverflow90(result, width, height)
      }

      if (rotate === 270) {
        result = await this.cutOverflow270(result, width, height)
      }

      return result
    },
    async cutOverflow90Horizontal (imgSrc, oldWidth, oldHeight) {
      const img = await this.loadIMG(imgSrc)
      const canvas = document.createElement('canvas')
      canvas.width = oldHeight
      canvas.height = oldWidth
      const ctx = canvas.getContext('2d')
      ctx.drawImage(img, oldWidth-oldHeight, 0, oldHeight, oldWidth, 0, 0, oldHeight, oldWidth)
      return canvas.toDataURL()
    },
    async cutOverflow270Horizontal (imgSrc, oldWidth, oldHeight) {
      const img = await this.loadIMG(imgSrc)
      const canvas = document.createElement('canvas')
      canvas.width = oldHeight
      canvas.height = oldWidth
      const ctx = canvas.getContext('2d')
      ctx.drawImage(img, 0, 0, oldHeight, oldWidth, 0, 0, oldHeight, oldWidth)
      return canvas.toDataURL()
    },
    async cutOverflow90 (imgSrc, oldWidth, oldHeight) {
      const img = await this.loadIMG(imgSrc)
      const canvas = document.createElement('canvas')
      canvas.width = oldHeight
      canvas.height = oldWidth
      const ctx = canvas.getContext('2d')
      ctx.drawImage(img, 0, 0, oldHeight, oldWidth, 0, 0, oldHeight, oldWidth)
      return canvas.toDataURL()
    },
    async cutOverflow270 (imgSrc, oldWidth, oldHeight) {
      const img = await this.loadIMG(imgSrc)
      const canvas = document.createElement('canvas')
      canvas.width = oldHeight
      canvas.height = oldWidth
      const ctx = canvas.getContext('2d')
      ctx.drawImage(img, 0, canvas.width-oldWidth, oldHeight, oldWidth, 0, 0, oldHeight, oldWidth)
      return canvas.toDataURL()
    },
    loadIMG (imgSrc) {
      const img = new Image()
      img.src = imgSrc
      return new Promise((resolve, reject) => {
        img.onload = () => {
          resolve(img)
        }
        img.onerror = () => {
          reject(img)
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.home {
  div {
    display: inline-block;
    vertical-align: top;
    img {
      background-color: rgba(0, 0, 0, .2);
    }
  }
}
</style>

