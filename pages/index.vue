<template>
  <div class="container">
    <div>
      <logo />
      <h1 class="title">
        mirelplatform
      </h1>
      <h2 class="subtitle">
        Nuxt.js Frontend Service.
      </h2>
      <div class="links">
        <nuxt-link
          to="/mste"
          class="button--grey"
        >
          MSTE
        </nuxt-link>
        <a
          href="https://dev.azure.com/mireljp/"
          target="_blank"
          class="button--grey"
        >
          Azure DevOps project
        </a>
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
