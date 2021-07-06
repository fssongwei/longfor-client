<template>
  <a-card title="价格趋势预测" :hoverable="true">
    <template #extra>每10秒刷新一次</template>
    <div class="flex flex-col justify-center items-center">
      <h3>{{ name + (type ? ` (${type})` : "") }}</h3>
      <line-chart :prices="prices"></line-chart>
    </div>
  </a-card>
</template>

<script lang="ts">
import { Options, Vue } from "vue-property-decorator";
import { Card } from "ant-design-vue";
import LineChart from "./LineChart.vue";
import axios from "axios";

@Options({
  components: {
    LineChart,
    ACard: Card,
  },
})
export default class RecordCard extends Vue {
  prices = [];
  name = "";
  type = "";
  unit = "";

  async fetchData(): Promise<void> {
    try {
      const { data } = await axios.get("api/oneRecord");
      this.prices = data.prices;
      this.unit = data.unit;
      this.name = data.name;
      this.type = data.type;
    } catch (error) {
      console.log(error.toString());
    }
  }

  mounted(): void {
    this.$nextTick(() => {
      this.fetchData();
      setInterval(this.fetchData, 10000);
    });
  }
}
</script>

<style scoped>
.chartBox {
  max-width: 800px;
  width: 100%;
  /* flex: 1; */
  widows: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.priceBox {
  margin-left: 30px;
}

.forcastCard {
  width: 500px;
}
</style>
