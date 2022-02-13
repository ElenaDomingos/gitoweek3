<template>
  <div class="c-stories-slider" >
    <div class="stories-container">
      <ul class="stories-wrapper" ref="slider">
        <li class="single-storie"
        v-for="(trending, ndx) in trendings"
        :key="trending.id"
         ref="item"
        >
      <slide :data="getStoryData(trending)"
      :active="slideNdx === ndx"
      :loading="slideNdx === ndx && loading"
      :btnsShown="activeBtns"
      @onNextSlide="handleSlider(ndx + 1)"
      @onPrevSlide="handleSlider(ndx - 1)"
      @onProgressFinish="handleSlider(ndx+1)" />
</li>
  </ul>
    </div>
  </div>
</template>
<script>
import { mapState, mapActions } from 'vuex'
import { slide } from '../slide'
export default {
  name: 'storiesSlider',
  components: {
    slide

  },
  props: {
    initialSlide: {
      type: Number
    }
  },
  data () {
    return {
      slideNdx: 0,
      sliderPosition: 0,
      loading: false,
      btnsShown: true

    }
  },
  computed: {
    ...mapState({
      trendings: (state) => state.trendings.data
    }),

    activeBtns () {
      if (this.btnsShown === false) return []
      if (this.slideNdx === 0) return ['next']
      if (this.slideNdx === this.trendings.lenght - 1) return ['prev']
      return ['next', 'prev']
    }
  },
  methods: {
    ...mapActions({
      fetchTrendings: 'trendings/fetchTrendings',
      fetchReadme: 'trendings/fetchReadme'
    }),
    async fetchReadmeForActiveSlide () {
      const { id, owner, name } = this.trendings[this.slideNdx]
      await this.fetchReadme({ id, owner: owner.login, repo: name })
    },
    getStoryData (obj) {
      console.log('readme ' + obj.readme)
      return {
        id: obj.id,
        userAvatar: obj.owner?.avatar_url,
        username: obj.owner?.login,
        content: obj.readme
      }
    },
    moveSlider (slideNdx) {
      const { slider, item } = this.$refs
      console.log('item - ' + item)
      const slideWidth = parseInt(
        getComputedStyle(item[0]).getPropertyValue('width'), 10)
      this.slideNdx = slideNdx
      this.sliderPosition = -(slideWidth * slideNdx)

      slider.style.transform = `translateX(${this.sliderPosition})`
    },
    async loadReadme () {
      this.loading = true
      this.btnsShown = false
      try {
        await this.fetchReadmeForActiveSlide()
      } catch (e) {
        console.log(e)
        throw e
      } finally {
        this.loading = false
        this.btnsShown = true
      }
    },
    async handleSlider (slideNdx) {
      this.moveSlider(slideNdx)
      await this.loadReadme()
    }

  },
  async mounted () {
    if (this.initialSlide) {
      const ndx = this.trendings.findIndex(item => item.id === this.initialSlide)

      await this.handleSlider(ndx)
    }
  },
  async created () {
    await this.fetchTrendings()
    await this.loadReadme()
  }

}
</script>

<style lang="scss">
.stories-wrapper {
  width:4000px;
  display:flex;
  align-items: center;
  width:auto;
  overflow:hidden;
  position:absolute;
}
.stories-container {
  position: relative;
}
</style>
