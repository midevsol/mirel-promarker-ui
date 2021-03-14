<template>
  <div class="container">
    <div class="container_title">
      AppRunner
    </div>
    <div class="inner">
      <div class="rightitems">
        <b-button :disabled="disabled || processing" variant="secondary">
          📄全てクリア
        </b-button>
        <b-button v-b-modal.modal-psv-dialog :disabled="disabled || processing" variant="secondary">
          📎Yaml形式
        </b-button>
      </div>
      <hr>
    </div>
  </div>
</template>

<script>
// import axios from 'axios'
// import BvUpload from '~/components/dialog/BvDownloadDialog.vue'

export default {
  layout: 'Main',
  components: {},
  data () {
    return {
    }
  },
  created () {
  },
  methods: {}
}
</script>

<style lang="css" scoped>
#fm_notes {
  white-space: pre-wrap; /* なんでダメなんだぁ。 */
}
</style>
