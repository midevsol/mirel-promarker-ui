<template>
  <div>
    <bv-download-dialog />
    <common-header />
    <nuxt />
    <common-footer />
  </div>
</template>

<script>
import CommonHeader from '~/components/frame/Header.vue'
import CommonFooter from '~/components/frame/Footer.vue'
import BvDownloadDialog from '~/components/dialog/BvDownloadDialog.vue'

export default {
  name: 'Main',
  components: {
    CommonHeader,
    CommonFooter,
    BvDownloadDialog
  }
}
</script>
