<template>
  <div class="container">
    <div class="container_title">
      MSTE 払出画面
    </div>
    <div class="inner">
      <div class="rightitems">
        <b-button v-b-modal.modal-psv-dialog :disabled="disabled || processing || stencilNoSelected" variant="secondary">
          📎Json形式
        </b-button>
        <b-button :disabled="disabled || processing || stencilNoSelected" variant="secondary" @click="clearDelems()">
          📄ステンシル定義を再取得
        </b-button>
        <b-button :disabled="disabled || processing" variant="secondary" @click="clearAll()">
          📄全てクリア
        </b-button>
        <b-button disabled variant="secondary" @click="callHistory()">
          🕒実行履歴
        </b-button>
      </div>
      <hr>
      <div>
        <form ref="form1" @submit.stop.prevent="mainHandleSubmit">
          <b-form-group
            invalid-feedback="Required item error."
          >
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
                    :disabled="disabled || processing"
                    required
                    @change="stencilCategorySelected()"
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
                    :disabled="disabled || processing || cateogryNoSelected"
                    required
                    @change="stencilSelected()"
                  />
                </b-col>
              </b-row>
              <b-row class="my-1">
                <b-col sm="3">
                  <label v-if="stencilConfig.description !== null" for="head_stencil_cd"> ステンシルについて</label>
                </b-col>
                <b-col sm="9" style="text-align:left">
                  <span v-if="stencilConfig.description !== null">
                    {{ stencilConfig.description }}
                  </span>
                </b-col>
              </b-row>
              <b-row class="my-1">
                <b-col sm="7" />
                <b-col sm="1">
                  <label for="head_serial_no">シリアル</label>
                </b-col>
                <b-col sm="4">
                  <b-form-select
                    id="head_serial_no"
                    v-model="fltStrSerialNo.selected"
                    :options="fltStrSerialNo.items"
                    :disabled="disabled || processing || stencilNoSelected"
                    required
                    @change="serialSelected()"
                  />
                </b-col>
              </b-row>
              <b-row class="my-1">
                <b-col sm="3" />
                <b-col sm="9" style="text-align:right">
                  <span v-if="stencilConfig.lastUpdateUser !== null">
                    Stencil Updated by {{ stencilConfig.lastUpdateUser }}
                  </span>
                  <br>
                </b-col>
              </b-row>
              <hr>
              <legend>データエレメント</legend>
              <b-row v-for="eparam in eparams" :key="eparam.id" class="my-1">
                <b-col sm="3">
                  <label :for="`eparam-${eparam.id}`">{{ eparam.name }}</label>
                </b-col>
                <b-col sm="4">
                  <b-form-input
                    :id="`eparam-${eparam.id}`"
                    v-model="eparam.value"
                    :placeholder="eparam.placeholder"
                    :disabled="disabled || processing"
                    required
                  />
                </b-col>
                <b-col sm="5" class="fm_notes">
                  <span>{{ eparam.note }}</span>
                </b-col>
              </b-row>
            </b-container>
            <hr>
            <b-button :disabled="disabled || processing || stencilNoSelected" variant="primary" @click="generate()">
              Generate
            </b-button>
            <hr>
          </b-form-group>
        </form>
      </div>
    </div>
    <div>
      <b-modal
        id="modal-psv-dialog"
        ref="modal"
        title="実行条件（JSON形式）"
        ok-title="Apply"
        cancel-title="Cancel"
        centered
        scrollable
        size="lg"
        @show="psvResetModal"
        @hidden="psvResetModal"
        @ok="psvHandleOk"
      >
        <form ref="form" @submit.stop.prevent="psvHandleSubmit">
          <b-form-group
            :state="psvState"
            label="JSON形式で実行条件を編集できます。"
            label-for="name-input"
            invalid-feedback="Json is required"
          >
            <b-form-textarea
              id="name-input"
              v-model="psvBody"
              rows="15"
              :state="psvState"
              required
              placeholder="input json"
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
      stencilNoSelected: true,
      cateogryNoSelected: true,
      eparams: [],
      stencilConfig: this.defaultStencilConfig,
      fltStrStencilCategory: {
        'selected': '',
        'items': []
      },
      fltStrStencilCd: {
        'selected': '',
        'items': []
      },
      fltStrSerialNo: {
        'selected': '',
        'items': []
      },
      psvBody: '',
      psvState: null
    }
  },
  created () {
    // refresh
    this.clearAll()
  },
  methods: {
    async refresh () {
      this.processing = true
      this.clearParams()
      await axios.post(
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

        if (!resp.data.model.params === false) {
          Object.assign(this.eparams, resp.data.model.params.childs)
        }
        if (!resp.data.model.stencil === false &&
          !resp.data.model.stencil.config === false) {
          this.stencilConfig = resp.data.model.stencil.config
        }
        this.fltStrStencilCategory = resp.data.model.fltStrStencilCategory
        this.fltStrStencilCd = resp.data.model.fltStrStencilCd
        this.fltStrSerialNo = resp.data.model.fltStrSerialNo
        this.processing = false
      }).catch((errors) => {
        this.bvMsgBoxErr(errors)
        this.processing = false
      })
    },

    clearDelems () {
      this.clearParams()
      this.refresh()
    },

    clearAll () {
      this.fltStrStencilCategory.selected = '*'
      this.fltStrStencilCd.selected = '*'
      this.fltStrSerialNo.selected = '*'
      this.cateogryNoSelected = true
      this.stencilNoSelected = true
      this.clearParams()
      this.refresh()
    },

    clearParams () {
      this.eparams = []
      this.stencilConfig = this.defaultStencilConfig()
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
      pitems.stencilCategoy = body.fltStrStencilCategory.selected
      pitems.stencilCanonicalName = body.fltStrStencilCd.selected
      pitems.serialNo = body.fltStrSerialNo.selected

      const assigned = Object.assign(body.eparams)
        .filter((item) => {
          return !item.noSend
        })
      for (const key in assigned) {
        pitems[assigned[key].id] = assigned[key].value
      }
      return pitems
    },
    stencilCategorySelected () {
      this.fltStrStencilCd.selected = '*'
      this.fltStrSerialNo.selected = ''
      this.cateogryNoSelected = false
      this.refresh()
    },
    stencilSelected () {
      this.fltStrSerialNo.selected = ''
      this.refresh()
      this.stencilNoSelected = false
    },
    serialSelected () {
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

    defaultStencilConfig () {
      return {
        id: null,
        name: null,
        serial: null,
        lastUpdate: null,
        lastUpdateUser: null,
        description: null
      }
    },

    async jsonValueToParam (psvBody) {
      await this.clearAll
      const psvBodyObj = JSON.parse(psvBody)

      // category selected
      this.fltStrStencilCategory.selected = psvBodyObj.stencilCategory
      await this.refresh()

      // stencil selected
      this.fltStrStencilCd.selected = psvBodyObj.stencilCd
      await this.refresh()

      // serial selected
      this.fltStrSerialNo.selected = psvBodyObj.serialNo
      await this.refresh()

      const eparams = Object.assign(this.eparams)
      this.eparams = []
      for (const key in psvBodyObj.dataElements) {
        const id = psvBodyObj.dataElements[key].id
        const value = psvBodyObj.dataElements[key].value
        this.setEparamById(eparams, id, value)
      }
      Object.assign(this.eparams, eparams)
    },

    setEparamById (eparams, id, value) {
      for (const key in eparams) {
        if (id === eparams[key].id) {
          eparams[key].value = value
        }
      }
    },

    paramToJsonValue (eparams) {
      if (!this.fltStrStencilCategory.selected) {
        return {}
      }
      if (!this.fltStrStencilCd.selected) {
        return {}
      }

      const dataElements = []
      for (const key in eparams) {
        const item = {}
        item.id = eparams[key].id
        item.value = eparams[key].value
        dataElements.push(item)
      }
      return {
        stencilCategory: this.fltStrStencilCategory.selected,
        stencilCd: this.fltStrStencilCd.selected,
        serialNo: this.fltStrSerialNo.selected,
        dataElements
      }
    },
    psvCheckFormValidity () {
      const valid = this.$refs.form.checkValidity()
      this.psvState = valid
      return valid
    },
    psvResetModal () {
      this.psvBody = ''
      this.psvBody = JSON.stringify(this.paramToJsonValue(this.eparams), null, '  ')
      this.psvState = null
    },
    psvHandleOk (bvModalEvt) {
      bvModalEvt.preventDefault()
      this.psvHandleSubmit()
    },
    psvHandleSubmit () {
      this.processing = true
      if (!this.psvCheckFormValidity()) {
        return
      }
      this.jsonValueToParam(this.psvBody)
      this.$nextTick(() => {
        this.$refs.modal.hide()
      })
      this.processing = false
    }
  }
}
</script>

<style lang="css" scoped>
#fm_notes {
  white-space: pre-wrap; /* なんでダメなんだぁ。 */
}
</style>
