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
          ProMarker
        </nuxt-link>
        <div>
          <demo-ag-grid-1 />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import DemoAgGrid1 from '~/components/pane/DemoAgGrid1.vue'
export default {
  layout: 'Main',
  components: {
    DemoAgGrid1
  }
}
</script>
