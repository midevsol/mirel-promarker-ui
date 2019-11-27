<template>
  <div>
    <b-modal
      id="bv_dialog"
      ref="bv_dialog"
      size="lg"
      scrollable
      centered
      ok-only
      title="File download"
    >
      <p class="my-4">
        <ul>
          <template v-for="file in files">
            <li :key="file.fileId">
              <a href="javascript:void(0)" @click="download(file)">
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
import axios from 'axios'

export default {
  name: 'BvDownloadDialog',
  data () {
    return {
      disabled: false,
      processing: false,
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
    download (file) {
      /* eslint-disable no-console */
      console.log('File download will start...', file)
      /* eslint-enable no-console */
      this.callDownloadApi(file)
    },
    downloadAll () {
      this.callDownloadApi(this.files)
    },
    initialize () {
    },
    hideBvDownloadDialog () {
      this.files = []
      this.$refs.bv_dialog.hide()
    },
    callDownloadApi (files) {
      axios.post(
        `/mapi/commons/download`, {
          responseType: 'blob',
          content: files
        }).then((resp) => {
        const name = resp.headers['content-disposition'].split('=')[1]
        const type = resp.headers['content-type']
        const blob = new Blob([resp.data], { type })
        const link = document.createElement('a')
        link.href = window.URL.createObjectURL(blob)
        link.download = name
        link.click()
      }).catch((errors) => {
        this.bvMsgBoxErr(errors)
        this.processing = false
      })
    }
  }
}
</script>
