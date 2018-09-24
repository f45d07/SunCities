<script>
import { Line, mixins } from 'vue-chartjs'
import { EventBus } from './EventBus.js'

var yAxeL = [];

export default {
  extends: Line,
  methods: {
    renderLineChart: function() {
        this.renderChart(this.chartData, {
            
         responsive: true, 
          maintainAspectRatio: false,
        scales: {
            xAxes: [{
                    type:       "time",
                    time:       {
                        unit: 'hour',
                        format: 'h:mm',
                    },
                    scaleLabel: {
                        display:     false,
                        labelString: 'Date'
                    }
                }],
            yAxes: [{
                ticks: {
                    steps: 2,
                                stepValue: 1,
                                max: 2.5,
                    // Include a dollar sign in the ticks
                    callback: function(value, index, values) {
                        return '';
                    }
                }
            }]
        }});
    }
  },
  mounted: function() {
    // this.chartData is created in the mixin.
    // If you want to pass options please create a local options object
    this.renderLineChart();
    EventBus.$on('chartdata', chartdata => {
        this.chartData = chartdata;
        this.renderLineChart();
    });
  }
}
</script>

<style>

</style>