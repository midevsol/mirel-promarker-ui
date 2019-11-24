<template>
  <div class="container">
    <div class="container_title">
      MSTE
    </div>
    <div class="inner">
      <div class="rightitems">
        <b-button variant="secondary" @click="refresh()">
          Refresh
        </b-button>
        <b-button variant="secondary" @click="callHistory()">
          Call history
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
        <b-button variant="primary" @click="generate(eparams)">
          Generate
        </b-button>
        <b-button variant="secondary">
          Show parameter as JSON
        </b-button>
        <hr>
      </div>
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
