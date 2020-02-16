<template>
  <div class="container">
    <div>
      <h1 class="title">
        mirelplatform
      </h1>
      <h2 class="subtitle">
        frontend service
      </h2>
      <div class="links">
        <nuxt-link
          to="/mste"
          class="button--grey"
        >
          MSTE
        </nuxt-link>
        <nuxt-link
          to="/qdev"
          class="button--grey"
        >
          品質開発（テスト観点）
        </nuxt-link>
        <a
          href="https://dev.azure.com/mireljp/"
          target="_blank"
          class="button--grey"
        >
          mireljp-dev
        </a>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  layout: 'Main',
  components: {
  }
}
</script>

<style lang="css">
</style>
