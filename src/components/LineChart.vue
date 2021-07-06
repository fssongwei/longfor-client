<template>
  <div class="chart">
    <canvas ref="chartRef"></canvas>
  </div>
</template>

<script lang="ts">
import { Options, Prop, Ref, Vue, Watch } from "vue-property-decorator";
import {
  Chart,
  LineElement,
  PointElement,
  LineController,
  CategoryScale,
  LinearScale,
  TimeScale,
  TimeSeriesScale,
  Decimation,
  Filler,
  Legend,
  Title,
  Tooltip,
  ChartItem,
  ChartConfiguration,
} from "chart.js";

Chart.register(
  LineElement,
  PointElement,
  LineController,
  CategoryScale,
  LinearScale,
  TimeScale,
  TimeSeriesScale,
  Decimation,
  Filler,
  Legend,
  Title,
  Tooltip
);

const MONTHS = [
  "202004",
  "202005",
  "202006",
  "202007",
  "202008",
  "202009",
  "202010",
  "202011",
  "202012",
  "202101",
  "202102",
  "202103",
  "202104",
  "202105",
  "202106",
  "202107",
  "202108",
  "202109",
];

let _chart = {} as Chart;

@Options({
  components: {},
})
export default class RecordCard extends Vue {
  @Prop({ type: Array, default: "" }) prices!: Array<number>;
  @Ref() chartRef!: Chart;

  forcastPrices = [];
  @Watch("prices")
  handleChange(arr: Array<number>): void {
    _chart.data.datasets[0].data = arr;
    _chart.update();
  }

  mounted(): void {
    const data = {
      labels: MONTHS,
      datasets: [
        {
          label: "价格",
          borderColor: "rgb(75, 192, 192)",
          data: this.prices,
          fill: false,
          segment: {
            borderDash: (ctx: any) =>
              ctx.p0.parsed.x > 13 ? [6, 6] : undefined,
          },
        },
      ],
    };

    const config = {
      type: "line",
      data,
      options: {},
    };

    const chart = new Chart(
      this.chartRef as ChartItem,
      config as ChartConfiguration
    );
    _chart = chart;
  }
}
</script>

<style scoped>
.chart {
  width: 100%;
}
</style>
