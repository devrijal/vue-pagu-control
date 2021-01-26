<template>
  <div class="main-box small-graph-box" v-if="show"
       :class="{'green-bg': percentage >= 90 && percentage <= 100, 'yellow-bg': percentage < 90, 'red-bg': percentage > 100}">
    <span class="value">{{ paguTotal }}</span>
    <span class="headline">TOTAL PAGU</span>
    <div class="progress">
      <div :style="`width: ${percentage}%;`" role="progressbar" class="progress-bar">
        <span class="sr-only">{{ percentage }}% used</span>
      </div>
    </div>
    <span class="subinfo">
				<i class="fa fa-money"></i> of <b>{{ control }}</b> allocated PAGU
			</span>
    <span class="subinfo">
				<i class="fa"
           :class="{'fa-arrow-circle-o-up': !exceed, 'fa-arrow-circle-o-down': exceed}">
        </i> <b>{{ remaining }}</b> PAGU {{exceed ? 'exceeds' : 'remaining'}}
			</span>
  </div>
</template>

<script>
module.exports = {
  name: "pagu-total",
  props: {
    getUrl: String,
    getCmd: {
      type: String,
      default: 'paguTotal'
    }
  },
  methods: {
    getData() {
      const url = `${this.getUrl}/${this.getCmd}`;
      axios.get(url)
          .then(resp => {
            this.data = resp.data;
            this.show = true;
          }).catch(e => console.log(e))
    },
    controlChanged(value) {
      const val = {value: value}
      Vue.set(this.data, 'paguControl', val)
    }
  },
  computed: {
    remaining() {
      if (this.data.hasOwnProperty('paguTotal') && this.data.hasOwnProperty('paguControl')) {
        let intTotal = parseInt(this.data.paguTotal.value);
        let intControl = parseInt(this.data.paguControl.value);
        let value = intControl - intTotal;
        if (isNaN(value)) {
          value = 0;
        }
        if (value <= 0) {
          this.exceed = true;
        } else if(value >= 0) {
          this.exceed = false;
        }
        return (value).toLocaleString('id')
      }
      return 0;
    },
    control() {
      if (this.data.hasOwnProperty('paguControl')) {
        return (parseInt(this.data.paguControl.value).toLocaleString('id'));
      }
      return 0;
    },
    paguTotal() {
      if (this.data.hasOwnProperty('paguTotal')) {
        let int = parseInt(this.data.paguTotal.value);
        let value = (int).toLocaleString('id');
        if (isNaN(int)) {
          value = 0;
        }
        return value;
      }
      return 0;
    },
    percentage() {
      if (this.data.hasOwnProperty('paguTotal') && this.data.hasOwnProperty('paguControl')) {
        return (parseInt(this.data.paguTotal.value)/parseInt(this.data.paguControl.value)) * 100;
      }
      return 0;
    }
  },
  data() {
    return {
      data: {},
      show: true,
      exceed: false
    }
  },
  created() {
    this.getData();
    eventBus.$on('paguControlChanged', this.controlChanged)
  }
}
</script>

<style scoped>

</style>
