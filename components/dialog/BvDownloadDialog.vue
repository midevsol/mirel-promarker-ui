<template>
  <div>
    <b-modal
      id="bv_dialog"
      ref="bv_dialog"
      size="lg"
      scrollable
      centered
      ok-only
      title="File management"
    >
      <div v-if="uploadMode">
        ファイル管理ID： {{ fileId }}
        <b-form-file multiple v-model="uploadingFiles">
        </b-form-file>
        <div class="d-block">
          <b-button class="mt-2" variant="outline-info" @click="upload()">
            Upload
          </b-button>
        </div>
      </div>
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
      files: [],
      uploadMode: false,
      fileId: null,
      uploadingFiles: null
    }
  },
  mounted () {
    this.$root.$on('bv::show::modal', (bvEvent, param) => {
      this.files = []
      this.uploadMode = false
      if (!param.uploadMode === false) {
        this.uploadMode = param.uploadMode
      }
      if (!param.fileId === false) {
        this.fileId = param.fileId
      }
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
      const files = []
      files.push(file)
      this.callDownloadApi(files)
    },
    upload () {
      this.callUploadApi(this.uploadingFiles)
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
    callUploadApi (uploadingFiles) {
      const formData = new FormData()
      formData.append('file', uploadingFiles)
      axios.post(
        `/mapi/commons/upload`,
        formData,
        {
          headers: {
            'content-type': 'multipart/form-data'
          }
        }
      ).then((resp) => {
        if (!resp.data.errs === false &&
          resp.data.errs.length > 0) {
          this.bvMsgBoxErr(resp.data.errs)
          this.processing = false
        }
        this.uploadingFiles = null
      })
    },
    callDownloadApi (files) {
      axios.post(
        `/mapi/commons/download`, {
          content: files
        }, {
          'responseType': 'blob'
        }).then((resp) => {
        if (!resp.data.errs === false &&
          resp.data.errs.length > 0) {
          this.bvMsgBoxErr(resp.data.errs)
          this.processing = false
          return
        }

        const name = resp.headers['content-disposition'].split('=')[1]
        const type = resp.headers['content-type']
        const blob = new Blob([resp.data], { type })
        const url = (window.URL || window.webkitURL).createObjectURL(blob)

        const link = document.createElement('a')
        link.href = url
        link.download = decodeURI(name)
        document.body.appendChild(link)
        link.click()
        document.body.removeChild(link)
      }).catch((errors) => {
        /* eslint-disable no-console */
        console.log('Cougth error.', errors)
        /* eslint-enable no-console */
        this.bvMsgBoxErr(errors)
        this.processing = false
      })
    },
    bvMsgBoxErr (message) {
      if (!message || message === undefined) {
        message = 'エラーが発生しました。管理者に問い合わせてください。'
      }
      this.$bvModal.msgBoxOk(message, {
        title: 'Error',
        size: 'lg',
        okTitle: 'Close',
        headerBgVariant: 'danger',
        headerTextVariant: 'light',
        footerBgVariant: 'light',
        scrollable: true,
        centered: true
      })
    }
  }
}
</script>
