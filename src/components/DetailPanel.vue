<template>
  <a-card :title="name" :bordered="false">
    <div v-if="loading">loading ...</div>
    <div v-if="!loading">
      <div class="grid grid-cols-2">
        <statistic title="本月价格" :value="'￥' + prices[prices.length - 4]" />
        <statistic title="上月价格" :value="'￥' + prices[prices.length - 5]" />
      </div>

      <div class="grid grid-cols-2">
        <statistic
          :title="changeRateLast >= 0 ? '同比增长' : '同比下降'"
          :value="changeRateLast * 100"
          :precision="2"
          suffix="%"
          :value-style="{ color: changeRateLast >= 0 ? 'red' : 'green' }"
          style="margin-right: 50px"
        >
          <template #prefix>
            <arrow-up-outlined v-if="changeRateLast >= 0" />
            <arrow-down-outlined v-if="changeRateLast < 0" />
          </template>
        </statistic>
        <statistic
          :title="changeRateCircle >= 0 ? '环比增长' : '环比下降'"
          :value="changeRateCircle * 100"
          :precision="2"
          suffix="%"
          :value-style="{ color: changeRateCircle >= 0 ? 'red' : 'green' }"
          style="margin-right: 50px"
        >
          <template #prefix>
            <arrow-up-outlined v-if="changeRateCircle >= 0" />
            <arrow-down-outlined v-if="changeRateCircle < 0" />
          </template>
        </statistic>
      </div>
    </div>
  </a-card>
</template>

<script lang="ts">
import { Options, Vue } from "vue-property-decorator";
import { Statistic, Card } from "ant-design-vue";
import { ArrowUpOutlined, ArrowDownOutlined } from "@ant-design/icons-vue";
import axios from "axios";

@Options({
  components: {
    Statistic,
    ArrowUpOutlined: ArrowUpOutlined,
    ArrowDownOutlined: ArrowDownOutlined,
    ACard: Card,
  },
})
export default class RecordCard extends Vue {
  realTimePrice = -1;
  realTimeUnit = "";
  loading = true;
  name = "";
  type = "";
  unit = "";
  prices: Array<number> = [];

  get changeRateCircle(): number {
    if (this.prices.length === 0) return 0;
    return (
      (this.prices[this.prices.length - 4] -
        this.prices[this.prices.length - 5]) /
      this.prices[this.prices.length - 4]
    );
  }

  get changeRateLast(): number {
    if (this.prices.length === 0) return 0;
    return (
      (this.prices[this.prices.length - 4] -
        this.prices[this.prices.length - 16]) /
      this.prices[this.prices.length - 4]
    );
  }

  async mounted(): Promise<void> {
    try {
      const { data } = await axios.get("api/oneRecord");

      this.name = data.name;
      this.unit = data.unit;
      this.type = data.type;
      this.prices = data.prices;
    } catch (error) {
      console.log(error.toString());
    }
    this.loading = false;
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

.container {
  display: flex;
}

.priceBox {
  margin-left: 30px;
}
</style>
