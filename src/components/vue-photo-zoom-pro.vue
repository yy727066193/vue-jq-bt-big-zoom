<template>
  <div class="pic-img">
    <div
      class="img-container"
      @mousemove="!moveEvent && mouseMove($event)"
      @mouseleave="!leaveEvent && mouseLeave($event)" >
      <img
        ref="img"
        @load="imgLoaded"
        :src="url"
        style="max-width:100%" />
      <div
        v-if="hideZoom && imgLoadedFlag &&!hideSelector"
        class="img-selector"
        :style="[imgSelectorSize, imgSelectorPosition, !outShow && imgBg, !outShow && imgBgSize, !outShow && imgBgPosition]"
      >
        <slot></slot>
      </div>
    </div>
  </div>

</template>
<script>
export default {
  name: 'vue-photo-zoom-pro',
  props: {
    url: String,
    highUrl: String,
    width: {
      type: Number,
      default: 168
    },
    outShowStyle: {},
    scale: {
      type: Number,
      default: 3
    },
    moveEvent: {
      type: [Object, MouseEvent],
      default: null
    },
    leaveEvent: {
      type: [Object, MouseEvent],
      default: null
    },
    outShow: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      selector: {
        width: this.width,
        top: 0,
        left: 0,
        bgTop: 0,
        bgLeft: 0,
        rightBound: 0,
        bottomBound: 0,
        absoluteLeft: 0,
        absoluteTop: 0
      },
      imgInfo: {},
      $img: null,
      screenWidth: document.body.clientWidth,
      outShowInitTop: 0,
      outShowTop: 0,
      imgLoadedFlag: false,
      hideSelector: false,
      hideZoom: false,
      timer: null
    }
  },
  watch: {
    moveEvent (e) {
      this.mouseMove(e)
    },
    leaveEvent (e) {
      this.mouseLeave(e)
    },
    url () {
      this.imgLoadedFlag = false
    },
    width (n) {
      this.initSelectorProperty(n)
    },
    screenWidth (val) {
      if (!this.timer) {
        this.screenWidth = val
        this.timer = setTimeout(() => {
          this.imgLoaded()
          clearTimeout(this.timer)
          this.timer = null
        }, 400)
      }
    }
  },
  computed: {
    addWidth () {
      return !this.outShow ? (this.width / 2) * (1 - this.scale) : 0
    },
    imgSelectorPosition () {
      let { top, left } = this.selector
      return {
        top: `${top}px`,
        left: `${left}px`
      }
    },
    imgSelectorSize () {
      let width = this.selector.width
      return {
        width: `${width}px`,
        height: `${width}px`
      }
    },
    imgBg () {
      return {
        backgroundImage: `url(${this.highUrl || this.url})`
      }
    },
    imgBgSize () {
      let {
        scale,
        imgInfo: { height, width }
      } = this
      return {
        backgroundSize: `${width * scale}px ${height * scale}px`
      }
    },
    imgBgPosition () {
      let { bgLeft, bgTop } = this.selector
      return {
        backgroundPosition: `${bgLeft}px ${bgTop}px`
      }
    }
  },
  mounted () {
    this.$img = this.$refs['img']
  },
  methods: {
    imgLoaded () {
      let imgInfo = this.$img.getBoundingClientRect()
      if (JSON.stringify(this.imgInfo) !== JSON.stringify(imgInfo)) {
        this.imgInfo = imgInfo
        this.initSelectorProperty(this.width)
        this.resetOutShowInitPosition()
      }
      if (!this.imgLoadedFlag) {
        this.imgLoadedFlag = true
        this.$emit('created', imgInfo)
      }
    },
    mouseMove (e) {
      this.hideZoom = true
      if (this.hideZoom && this.imgLoadedFlag) {
        this.imgLoaded()
        const { pageX, pageY, clientY } = e
        const { scale, selector, outShow, addWidth } = this
        let { outShowInitTop } = this
        const scrollTop = pageY - clientY
        const { absoluteLeft, absoluteTop, rightBound, bottomBound } = selector
        const x = pageX - absoluteLeft // 选择器的x坐标 相对于图片
        const y = pageY - absoluteTop // 选择器的y坐标
        if (outShow) {
          if (!outShowInitTop) {
            outShowInitTop = this.outShowInitTop = scrollTop + this.imgInfo.top
          }
          this.hideOutShow && (this.hideOutShow = false)
          this.outShowTop =
            scrollTop > outShowInitTop ? scrollTop - outShowInitTop : 0
        }
        this.hideSelector && (this.hideSelector = false)
        selector.top = y > 0 ? (y < bottomBound ? y : bottomBound) : 0
        selector.left = x > 0 ? (x < rightBound ? x : rightBound) : 0
        selector.bgLeft = addWidth - x * scale // 选择器图片的坐标位置
        selector.bgTop = addWidth - y * scale
      }
    },
    initSelectorProperty (selectorWidth) {
      const selectorHalfWidth = selectorWidth / 2
      const selector = this.selector
      const { width, height, left, top } = this.imgInfo
      const { scrollLeft, scrollTop } = document.documentElement
      selector.width = selectorWidth
      selector.rightBound = width - selectorWidth
      selector.bottomBound = height - selectorWidth
      selector.absoluteLeft = left + selectorHalfWidth + scrollLeft
      selector.absoluteTop = top + selectorHalfWidth + scrollTop
    },
    mouseLeave () {
      this.hideSelector = true
      if (this.outShow) {
        this.hideOutShow = true
      }
    },
    reset () {
      Object.assign(this.selector, {
        top: 0,
        left: 0,
        bgLeft: 0,
        bgTop: 0
      })
      this.resetOutShowInitPosition()
    },
    resetOutShowInitPosition () {
      this.outShowInitTop = 0
    }
  }
}
</script>

<style scoped>
.img-container {
  position: relative;
}

.img-selector {
  position: absolute;
  cursor: crosshair;
  border: 1px solid rgba(0, 0, 0, 0.1);
  background-repeat: no-repeat;
  background-color: rgba(0, 0, 0, 0.6);
}
</style>
