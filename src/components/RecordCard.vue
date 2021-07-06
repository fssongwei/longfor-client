<template>
  <div class="container">
    <div class="chartBox">
      <h5>往期15个月价格 + 未来3月价格预测</h5>
      <line-chart :prices="prices"></line-chart>
    </div>
    <div class="priceBox">
      <statistic title="实时价格" :value="displayPrice" />

      <statistic title="本月价格" :value="'￥' + prices[prices.length - 4]" />
      <statistic
        title="预估下月价格"
        :value="'￥' + prices[prices.length - 1]"
      />

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
</template>

<script lang="ts">
import { Options, Prop, Ref, Vue } from "vue-property-decorator";
import { Statistic } from "ant-design-vue";
import { ArrowUpOutlined, ArrowDownOutlined } from "@ant-design/icons-vue";
import LineChart from "./LineChart.vue";
import axios from "axios";

@Options({
  components: {
    LineChart,
    Statistic,
    ArrowUpOutlined: ArrowUpOutlined,
    ArrowDownOutlined: ArrowDownOutlined,
  },
})
export default class RecordCard extends Vue {
  @Prop({ type: String, default: "" }) name!: string;
  @Prop({ type: String, default: "" }) type!: string;
  @Prop({ type: String, default: "" }) id!: string;
  @Prop({ type: String, default: "" }) unit!: string;
  @Prop({ type: Array, default: "" }) prices!: Array<number>;
  @Ref() chartRef!: HTMLElement;

  realTimePrice = -1;
  realTimeUnit = "";
  loadingPrice = true;

  get displayPrice(): string {
    if (this.loadingPrice) return "正在获取实时价格 ...";
    if (this.realTimePrice === -1 || !this.realTimePrice) return "暂无数据";
    let str = `￥${this.realTimePrice}`;
    if (this.realTimeUnit) {
      str += ` / ${this.realTimeUnit}`;
    }
    return str;
  }

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
      const { data } = await axios.get("api/realTimePrice", {
        params: { search: this.name },
      });

      this.realTimePrice = data.price;
      this.realTimeUnit = data.unit;
    } catch (error) {
      console.log(error.toString());
    }
    this.loadingPrice = false;
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
