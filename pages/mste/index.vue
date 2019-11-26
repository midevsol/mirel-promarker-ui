<template>
  <div class="container">
    <div class="container_title">
      MSTE
    </div>
    <div class="inner">
      <div class="rightitems">
        <b-button v-b-modal.modal-psv-dialog variant="secondary">
          Json形式でパラメータを入力する
        </b-button>
        <b-button variant="secondary" @click="refresh()">
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
              />
            </b-col>
          </b-row>
          <hr>
          <legend>業務オブジェクト</legend>
          <b-row v-for="eparam in eparams" :key="eparam.id" class="my-1">
            <b-col sm="3">
              <label :for="`eparam-${eparam.id}`">{{ eparam.name }}</label>
            </b-col>
            <b-col sm="4">
              <b-form-input :id="`eparam-${eparam.id}`" v-model="eparam.value" placeholder="Input value." />
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
        <form ref="form" @submit.stop.prevent="handleSubmit">
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
      eparams: [],
      fltStrStencilCategory: {
        'selected': '',
        'items': []
      },
      fltStrStencilCd: {
        'selected': '',
        'items': []
      }
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
      axios.post(
        `/api/mste/suggest`,
        { content: this.createRequest(this) }
      )
        .then((resp) => {
          Object.assign(this.eparams, resp.data.model.params.childs)
          this.fltStrStencilCategory = resp.data.model.fltStrStencilCategory
          this.fltStrStencilCd = resp.data.model.fltStrStencilCd
        })
    },

    callHistory () {
    },

    generate (eparams) {
      axios.post(
        `/api/mste/generate`,
        { content: this.createRequest(this) }
      ).then((resp) => {
        this.$root.$emit('bv::show::modal', 'bv_dialog', {
          files: [
            {
              fileId: '1',
              name: 'フィアウル1'
            },
            {
              fileId: '2',
              name: 'フィアウル2'
            },
            {
              fileId: '3',
              name: 'フィアウル3'
            }
          ]
        })
      })
    },

    createRequest (body) {
      const assigned = Object.assign(body.eparams)
        .filter((item) => {
          return !item.noSend
        })
      const pitems = []
      for (const key in assigned) {
        pitems.push({
          id: assigned[key].id,
          value: assigned[key].value
        })
      }
      return pitems
    }
  }
}
</script>

<style lang="css" scoped>

</style>
