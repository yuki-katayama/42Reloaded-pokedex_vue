<script>
import { Radar } from 'vue-chartjs';

export default {
  extends: Radar,
  props: {
    statsData: {
      type: Array,
      default: null
    },
  },
  name: 'chart',
  data () {
    return {
      data: {
        labels: ['たいりょく', 'こうげき', 'ぼうぎょ', 'とくこう', 'とくぼう', 'すばやさ'],
        datasets: [
          {
            label: 'ステータス',
            data: this.statsData,
            backgroundColor: [
              'rgba(153, 102, 255, 0.2)',
            ],
            borderColor: [
              'rgba(153, 102, 255, 1)',
            ],
            borderWidth: 1,
          },
        ]
      },
    }
  },
  mounted () {
    
    this.renderChart(this.data, this.options)
  }
}
</script>