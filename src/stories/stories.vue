<template>
  <div class="wrapper">
    <header class="header">
      <div class="container">
        <div class="header-content">
          <div class="header__logo" @click="$router.push({ name: 'root' })">
            <h1 class="logo">Gitogram /</h1>
          </div>
          <button class="close" @click="$router.push({ name: 'root' })">
            <Icon name="close" />
          </button>
        </div>
      </div>
    </header>
    <div class="app-slider">
      <Slider
        :initialSlide="Number($route.params.initialSlide)"
        @noMoreSlides="$router.push({ name: 'root' })"/>
    </div>
  </div>
</template>

<script>

import { icon as Icon } from '../icons'
import { storiesSlider as Slider } from '../components/storiesSlider'
export default {
  name: 'Stories',
  components: {

    Icon,
    Slider
  }
}
</script>

<style lang="scss" scoped>
.wrapper, .app-slider {
  background:#000;
}
.logo {
  color:#fff;
}
.close {
  width:50px;
}
</style>
