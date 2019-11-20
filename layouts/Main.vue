<template>
  <div>
    <common-header />
    <nuxt />
  </div>
</template>

<script>
import CommonHeader from '~/components/frame/Header.vue'

export default {
  name: 'Main',
  components: {
    CommonHeader
  }
}
</script>

<style lang="css">
</style>
