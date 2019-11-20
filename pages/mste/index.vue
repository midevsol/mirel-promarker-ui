<template>
  <div class="container">
    <div class="container_title">
      MSTE
    </div>
    <div class="inner">
      <div class="rightitems">
        <button type="button" class="btn btn-secondary">
          Initialize items
        </button>
        <button type="button" class="btn btn-secondary">
          Call history
        </button>
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
              <b-form-select id="head_stencil_kind" v-model="fltStrStencilKinds.selected">
                <option
                  v-for="(option) in fltStrStencilKinds.options"
                  :key="option.text"
                >
                  {{ option.value }}
                </option>
              </b-form-select>
            </b-col>
          </b-row>
          <b-row class="my-1">
            <b-col sm="3">
              <label for="head_stencil_cd">ステンシル</label>
            </b-col>
            <b-col sm="9">
              <b-form-select id="head_stencil_cd" v-model="fltStrStencilCds.selected">
                <option
                  v-for="(option) in fltStrStencilCds.options"
                  :key="option.text"
                >
                  {{ option.value }}
                </option>
              </b-form-select>
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
        <button type="button" class="btn btn-primary">
          Generate
        </button>
        <hr>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  layout: 'Main',
  data () {
    return {
      eparams: [
        {
          'id': 'applicationId',
          'name': 'アプリケーションID',
          'valueType': 'text',
          'value': 'biz',
          'note': 'アプリケーションのIDを指定してください。例： biz, sms, fms･･･',
          'sort': 10
        },
        {
          'id': 'applicationName',
          'name': '名称',
          'valueType': 'text',
          'value': 'Biz∫',
          'note': 'アプリケーションの名称を入力してください。',
          'sort': 10
        }
      ],
      fltStrStencilKinds: {
        'selected': 'bizapp',
        'options': [
          {
            'text': 'bizapp',
            'value': 'Biz∫アプリケーション'
          }
        ]
      },
      fltStrStencilCds: {
        'selected': '19001',
        'options': [
          {
            'text': '19001',
            'value': 'Biz∫販売 バッチ処理（Leagcy）'
          }
        ]
      },
      types: [
        'text',
        'number',
        'email',
        'password',
        'search',
        'url',
        'tel',
        'date',
        'time',
        'range',
        'color'
      ]
    }
  },

  async asyncData ({ app }) {
    const url = `/mste/initialize`
    const data = await app.$axios.$get(url)
      .then((resp) => {
        return { datas: resp.data }
      })
    return data
  }
}
</script>

<style lang="css" scoped>

</style>
