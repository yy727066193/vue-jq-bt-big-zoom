<template>
  <div class="home">
    <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#exampleModal">
      Launch demo modal
    </button>

    <div class="modal fade bd-example-modal-lg" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-lg" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">Modal title</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <button type="button" class="btn btn-primary btn-sm" @click="addSelectorWidth">Add</button>
            <button type="button" class="btn btn-primary btn-sm" @click="subSelectorWidth">Sub</button>
            <div v-for="(item, index) in imgList" :key="index + ''">
              <vue-photo-zoom-pro
                :width="width"
                :url="item"
                :scale="scale">
              </vue-photo-zoom-pro>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
            <button type="button" class="btn btn-primary">Save changes</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import VuePhotoZoomPro from './vue-photo-zoom-pro'

export default {
  name: 'Home',
  components: {
    VuePhotoZoomPro
  },
  data () {
    return {
      index: 0,
      scale: 3,
      showType: false,
      width: 200,
      imgList: [
        'https://bpic.588ku.com/illus_water_img/18/07/30/f3c7060bc28216271dc8c4630b288331.jpg!/watermark/url/L3dhdGVyL3dhdGVyX2JhY2tfNDAwXzIwMC5wbmc=/repeat/true',
        'https://bpic.588ku.com/illus_water_img/18/08/21/64d2b3b02352b00dd668b705fd8af276.jpg!/watermark/url/L3dhdGVyL3dhdGVyX2JhY2tfNDAwXzIwMC5wbmc=/repeat/true',
        'https://bpic.588ku.com/illus_water_img/18/08/18/472f21d353b64c95b9994be6ed3b7274.jpg!/watermark/url/L3dhdGVyL3dhdGVyX2JhY2tfNDAwXzIwMC5wbmc=/repeat/true'
      ]
    }
  },
  methods: {
    addSelectorWidth () {
      this.width += 20
    },
    subSelectorWidth () {
      this.width -= 20
    }
  }
}
</script>
