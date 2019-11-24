<template>
  <div>
    <b-modal
      id="bv_dialog"
      ref="bv_dialog"
      size="xl"
      scrollable
      centered
      ok-only
      title="File download"
    >
      <p class="my-4">
        <ul>
          <template v-for="file in files">
            <li :key="file.fileId">
              <a href="javascript:void(0)" @click="download(file.fileId)">
                {{ file.name }}
              </a>
            </li>
          </template>
        </ul>
      </p>
      <div class="d-block">
        <b-button class="mt-2" variant="outline-info" @click="downloadAll()">
          Download All
        </b-button>
      </div>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: 'BvDownloadDialog',
  data () {
    return {
      files: []
    }
  },
  mounted () {
    this.$root.$on('bv::show::modal', (bvEvent, param) => {
      this.files = []
      /* eslint-disable no-console */
      console.log('File download dialog created.', bvEvent, param.files)
      /* eslint-enable no-console */
      for (const key in param.files) {
        this.files.push({
          name: param.files[key].name,
          fileId: param.files[key].fileId
        })
      }
    })
  },
  methods: {
    download (fileId) {
      /* eslint-disable no-console */
      console.log('File download will start...', fileId)
      /* eslint-enable no-console */
    },
    downloadAll () {
    },
    initialize () {
    },
    showBvDownloadDialog (files) {
      this.files.push({
        name: 'フィアウル1',
        fileId: 'abcabc'
      })
      this.files.push({
        name: 'フィアウル2',
        fileId: 'defdef'
      })
      this.$refs.bv_dialog.show()
    },
    hideBvDownloadDialog () {
      this.files = []
      this.$refs.bv_dialog.hide()
    }
  }
}
</script>
