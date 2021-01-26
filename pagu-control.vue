<template>
  <b-spinner label="Spinning" v-if="loading"></b-spinner>
  <div class="main-box infographic-box colored emerald-bg pointer" v-else-if="editable" @click="confirm">
    <i class="fa fa-money"></i>
    <span class="headline">PAGU KONTROL</span>
    <span class="value">{{value}}</span>
  </div>
  <div class="main-box infographic-box colored emerald-bg" v-else>
    <i class="fa fa-money"></i>
    <span class="headline">PAGU KONTROL</span>
    <span class="value">{{value}}</span>
  </div>
</template>

<script>
module.exports = {
  name: "pagu-control",
  props: {
    getUrl: String,
    postUrl: String,
    getCmd: {
      type: String,
      default: 'paguControl'
    },
    postCmd: {
      type: String,
      default: 'paguControlSet'
    },
    editable: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    getData() {
      const url = `${this.getUrl}/${this.getCmd}`;
      axios.get(url)
          .then(resp => {
            this.data = resp.data;
            this.loading = false;
          }).catch(e => console.log(e))
    },
    setData() {
      this.loading = true;
      const url = `${this.postUrl}`;
      const data = {
        cmd: this.postCmd,
        data: {
          pagu: this.pagu
        }
      }
      axios.post(url, data)
          .then(resp => {
            if (resp.data.class === 'success') {
              Vue.set(this.data, 'value', this.pagu);
              eventBus.$emit('paguControlChanged', this.pagu);
            }
            this.loading = false;
            let notif;
            if (!resp.data) {
              notif = {
                class: 'danger',
                notification: 'PAGU cannot be saved due to server not responding',
                callback: 'infoSnackbar'
              }
            } else {
              notif = resp.data;
            }
            eventBus.$emit('openNotif', notif);
          }).catch(e => console.log(e))
    },
    confirm() {
      const self = this;
      if (this.data.hasOwnProperty('value')) {
        this.pagu = this.data.value;
      }
      let message = this._row([
          this._col([
            this.el('div', {class: 'form-group'}, [
              this.el('label', 'PAGU'),
              this.el('b-form-input', {
                props: {value: self.pagu, placeholder: 'Nilai PAGU kontrol', autofocus: true, formatter: self.numberOnly},
                on: {input: value => {self.pagu = value}},
              })
            ])
          ], 'col-md-12')]);
      this.$bvModal.msgBoxConfirm([message], {
        title: `Set PAGU Kontrol`,
        centered: true,
        size: 'sm',
        okTitle: 'Simpan',
        noCloseOnBackdrop: true,
        noCloseOnEsc: true
      })
          .then(ok => {
            if (ok) {
              this.setData();
            }
          }).catch(e => console.log(e));
    },
    _row(contents = [], _class = ['row']) {
      return this.el('div', {class: _class}, contents);
    },
    _col(contents = [], _class=null) {
      return this.el('div', {class: _class}, contents)
    },
    numberOnly(str) {
      return str.replace(/[^,\d]/g, '');
    }
  },
  computed: {
    value() {
      let result = 0;
      if (this.data.hasOwnProperty('value')) {
        result = (parseInt(this.data.value).toLocaleString('id'));
      }
      return result;
    }
  },
  data() {
    return {
      el: this.$createElement,
      data: {},
      pagu: 0,
      loading: true
    }
  },
  created() {
    this.getData();
  }
}
</script>

<style>
.pointer {
  cursor: pointer;
}
</style>