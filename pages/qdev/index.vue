<template>
  <div class="container">
    <div class="container_title">
      QDEV Workbench
    </div>
    <div class="inner">
      <div class="rightitems">
        <b-button :disabled="disabled || processing" @click="clearAll()" variant="secondary">
          📄全てクリア
        </b-button>
      </div>
      <hr>
      <div class="main">
        <div class="tdside">
          サイドバー
        </div>
        <div class="tdev">
          <div class="kate kate_1" />
          <div class="kate kate_2" />
          <div class="kate kate_2" />
          <br>
          <div class="kate kate_3_1" />
          <div class="kate kate_3_2" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import axios from 'axios'

export default {
  layout: 'Main',
  data () {
    return {
      disabled: false,
      processing: false,
      eparams: [],
      kate1: '目的機能名_event'
    }
  },

  methods: {

    clearAll () {
      return {}
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

    defaultStore () {
      return {
        'selected': '',
        'items': []
      }
    }
  }
}
</script>

<style lang="css" scoped>
#fm_notes {
  white-space: pre-wrap; /* なんでダメなんだぁ。 */
}

.kate_1 {
  width: 80%;
  height: 50px;
}

.kate_2 {
  width: 49.5%;
  height: 400px;
}

.kate_3_1 {
  width: 69%;
  height: 150px;
}

.kate_3_2 {
  width: 30%;
  height: 150px;
}

.kate {
  display: inline-block;
  margin: 0px;
  padding: 0px;
  border: 0.5px solid black;
  box-shadow: -3px -3px 4px 1px #525252;
  color: black;
  background-color: #8fccff;
}

.tdev {
  display: flow-root;
}

.tdside {
  float: right;
  border: 0.5px solid black;
  margin: 1px;
  width: 400px;
  height: 610px;
  position: sticky;
  background-color: #ffffff;
  box-shadow: -3px -3px 4px 1px #525252;
}

.main {
  display:table;
  width: 100%;
  height: 100%;
}
</style>
