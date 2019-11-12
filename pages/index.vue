<template>
  <div class="container">
    <div>
      <logo />
      <h1 class="title">
        mipla-v
      </h1>
      <h2 class="subtitle">
        mipla frontend project
      </h2>
      <div class="links">
        <a
          href="https://nuxtjs.org/"
          target="_blank"
          class="button--green"
        >
          Documentation
        </a>
        <a
          href="https://github.com/nuxt/nuxt.js"
          target="_blank"
          class="button--grey"
        >
          GitHub
        </a>
        <nuxt-link
          to="/mste"
          class="button--grey"
        >
          MSTE
        </nuxt-link>
      </div>
    </div>
  </div>
</template>

<script>
import Logo from '~/components/Logo.vue'

export default {
  layout: 'Main',
  components: {
    Logo
  }
}
</script>

<style lang="css">
</style>
