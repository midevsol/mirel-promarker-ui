<template>
  <div class="container">
    <div class="container_title">
      MSTE 払出画面
    </div>
    <div class="inner">
      <div class="rightitems">
        <b-button v-b-modal.modal-psv-dialog :disabled="disabled || processing" variant="secondary">
          Json形式でパラメータを入力する
        </b-button>
        <b-button :disabled="disabled || processing" variant="secondary" @click="refresh()">
          クリア
        </b-button>
        <b-button disabled variant="secondary" @click="callHistory()">
          実行履歴
        </b-button>
      </div>
      <hr>
      <div>
        <b-container fluid>
          <legend>ステンシル情報</legend>
          <b-row class="my-1">
            <b-col sm="3">
              <label for="head_stencil_kind">分類</label>
            </b-col>
            <b-col sm="9">
              <b-form-select
                id="head_stencil_kind"
                v-model="fltStrStencilCategory.selected"
                :options="fltStrStencilCategory.items"
                required
                @change="stencilSelected()"
              />
            </b-col>
          </b-row>
          <b-row class="my-1">
            <b-col sm="3">
              <label for="head_stencil_cd">ステンシル</label>
            </b-col>
            <b-col sm="9">
              <b-form-select
                id="head_stencil_cd"
                v-model="fltStrStencilCd.selected"
                :options="fltStrStencilCd.items"
                required
                @change="stencilSelected()"
              />
            </b-col>
          </b-row>
          <b-row class="my-1">
            <b-col sm="3" />
            <b-col sm="9" style="text-align:left">
              <span v-if="stencilConfig.description !== null">
                {{ stencilConfig.description }}
              </span>
            </b-col>
          </b-row>
          <b-row class="my-1">
            <b-col sm="3" />
            <b-col sm="9" style="text-align:right">
              <span v-if="stencilConfig.serial !== null">
                Stencil S/N：{{ stencilConfig.serial }}
              </span>
              <span v-if="stencilConfig.serial !== null && stencilConfig.astUpdate !== null">
                &nbsp;/&nbsp;
              </span>
              <span v-if="stencilConfig.lastUpdate !== null">
                Last update：{{ stencilConfig.lastUpdate }}
              </span>
              <span v-if="stencilConfig.lastUpdateUser !== null">
                by {{ stencilConfig.lastUpdateUser }}
              </span>
              <br>
            </b-col>
          </b-row>
          <hr>
          <legend>業務オブジェクト</legend>
          <b-row v-for="eparam in eparams" :key="eparam.id" class="my-1">
            <b-col sm="3">
              <label :for="`eparam-${eparam.id}`">{{ eparam.name }}</label>
            </b-col>
            <b-col sm="4">
              <b-form-input :id="`eparam-${eparam.id}`" v-model="eparam.value" :placeholder="eparam.placeholder" required />
            </b-col>
            <b-col sm="5" class="fm_notes">
              <span>{{ eparam.note }}</span>
            </b-col>
          </b-row>
        </b-container>
        <hr>
        <b-button :disabled="disabled || processing" variant="primary" @click="generate()">
          Generate
        </b-button>
        <hr>
      </div>
    </div>
    <div>
      <b-modal
        id="modal-psv-dialog"
        ref="modal"
        title="実行パラメータ情報"
        ok-title="Apply"
        cancel-title="Cancel"
        centered
        scrollable
        @show="psvResetModal"
        @hidden="psvResetModal"
        @ok="psvHandleOk"
      >
        <form ref="form" @submit.stop.prevent="psvHandleSubmit">
          <b-form-group
            :state="psvState"
            label="Json"
            label-for="name-input"
            invalid-feedback="Json is required"
          >
            <b-form-textarea
              id="name-input"
              v-model="psvBody"
              rows="5"
              :state="psvState"
              required
              placeholder="あぁごめんちゃい～
未実装にゃのだ～ =^_^="
            />
          </b-form-group>
        </form>
      </b-modal>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  layout: 'Main',
  data () {
    return {
      disabled: false,
      processing: false,
      eparams: [],
      stencilConfig: {
        id: null,
        name: null,
        serial: null,
        lastUpdate: null,
        lastUpdateUser: null,
        description: null
      },
      fltStrStencilCategory: {
        'selected': '',
        'items': []
      },
      fltStrStencilCd: {
        'selected': '',
        'items': []
      },
      psvBody: '',
      psvState: null
    }
  },
  params: {
    bvDownloadFiles: [
      {
        id: 'aidhi',
        name: 'namena'
      }
    ]
  },
  methods: {
    refresh () {
      this.processing = true
      axios.post(
        // `/api/mste/suggest`,
        '/mapi/apps/mste/api/suggest',
        { content: this.createRequest(this) }
      ).then((resp) => {
        if (!resp.data.errs === false &&
          resp.data.errs.length > 0) {
          this.bvMsgBoxErr(resp.data.errs)
          this.processing = false
          return
        }

        Object.assign(this.eparams, resp.data.model.params.childs)
        this.stencilConfig = resp.data.model.stencil.config
        this.fltStrStencilCategory = resp.data.model.fltStrStencilCategory
        this.fltStrStencilCd = resp.data.model.fltStrStencilCd
        this.processing = false
      }).catch((errors) => {
        this.bvMsgBoxErr(errors)
        this.processing = false
      })
    },

    callHistory () {
    },

    generate () {
      this.processing = true
      axios.post(
        `/mapi/apps/mste/api/generate`,
        { content: this.createRequest(this) }
      ).then((resp) => {
        /* eslint-disable no-console */
        console.log(resp)
        /* eslint-enable no-console */
        if (!resp.data.model) {
          this.processing = false
          return
        }

        if (!resp.data.errs === false &&
          resp.data.errs.length > 0) {
          this.bvMsgBoxErr(resp.data.errs)
          this.processing = false
          return
        }

        if (!resp.data.model.files === false) {
          const paramFiles = []
          for (const key in resp.data.model.files) {
            paramFiles[key] = {
              fileId: resp.data.model.files[key][0],
              name: resp.data.model.files[key][1]
            }
          }
          this.$root.$emit('bv::show::modal', 'bv_dialog', { files: paramFiles })
        }
        this.processing = false
      }).catch((errors) => {
        this.bvMsgBoxErr(errors)
        this.processing = false
      })
    },

    createRequest (body) {
      const pitems = {}
      pitems.stencilCanonicalName = body.fltStrStencilCd.selected

      const assigned = Object.assign(body.eparams)
        .filter((item) => {
          return !item.noSend
        })
      for (const key in assigned) {
        pitems[assigned[key].id] = assigned[key].value
      }
      return pitems
    },

    stencilSelected () {
      this.refresh()
    },

    bvMsgBoxErr (msgs) {
      if (!msgs || msgs === undefined) {
        msgs = 'エラーが発生しました。管理者に問い合わせてください。'
      }
      let converted = ''
      if (Array.isArray(msgs)) {
        for (const key in msgs) {
          converted += msgs[key]
          converted += ' '
        }
      } else {
        converted += msgs
      }

      this.$bvModal.msgBoxOk(converted, {
        title: 'Error',
        size: 'lg',
        okTitle: 'Close',
        headerBgVariant: 'danger',
        headerTextVariant: 'light',
        footerBgVariant: 'light',
        scrollable: true,
        centered: true
      })
    },
    psvCheckFormValidity () {
      const valid = this.$refs.form.checkValidity()
      this.psvState = valid
      return valid
    },
    psvResetModal () {
      this.psvBody = ''
      this.psvState = null
    },
    psvHandleOk (bvModalEvt) {
      bvModalEvt.preventDefault()
      this.psvHandleSubmit()
    },
    psvHandleSubmit () {
      if (!this.psvCheckFormValidity()) {
        return
      }
      // this.submittedNames.push(this.name)
      this.$nextTick(() => {
        this.$refs.modal.hide()
      })
    }
  }
}
</script>

<style lang="css" scoped>
#fm_notes {
  white-space: pre-wrap; /* なんでダメなんだぁ。 */
}
</style>
