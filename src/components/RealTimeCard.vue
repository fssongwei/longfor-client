<template>
  <a-card title="实时价格展示" :hoverable="true">
    <template #extra>
      <div class="flex items-center">
        上次更新: {{ lastUpdate }}
        <SyncOutlined class="ml-1" @click="fetchData" />
      </div>
    </template>
    <a-table
      :columns="columns"
      :data-source="displayData"
      :pagination="false"
    />
  </a-card>
</template>

<script lang="ts">
import { Options, Vue } from "vue-property-decorator";
import { Typography, Card, Table, Button } from "ant-design-vue";
import { SyncOutlined } from "@ant-design/icons-vue";
import LineChart from "./LineChart.vue";
import axios from "axios";

const names = [
  "粘贴并点锚装饰保温一体化板",
  "薄片花岗岩石铝蜂窝复合板",
  "干挂装饰保温一体化板",
];

const columns = [
  {
    title: "名称",
    dataIndex: "name",
    key: "name",
  },
  {
    title: "当前价格",
    dataIndex: "price",
    key: "price",
  },
];

@Options({
  components: {
    LineChart,
    ATypographyTitle: Typography.Title,
    ACard: Card,
    ATable: Table,
    AButton: Button,
    SyncOutlined,
  },
})
export default class RealTimeCard extends Vue {
  prices = [];
  name = "";
  type = "";
  unit = "";

  columns = columns;

  realTimePrice = -1;
  realTimeUnit = "";
  loading = true;
  lastUpdate = "暂无";

  error = "";
  displayData: Array<{ name: string; price: string }> = [];

  async fetchData(): Promise<void> {
    this.loading = true;
    let initDisplay = [];
    for (let searchTerm of names) {
      initDisplay.push({
        name: searchTerm,
        price: "正在实时获取 ...",
        key: searchTerm,
      });
    }
    this.displayData = initDisplay;

    let res = [];

    for (let searchTerm of names) {
      try {
        const { data } = await axios.get("api/realTimePrice", {
          params: { search: searchTerm },
        });

        let name = searchTerm;
        let price = data.price ? `￥${data.price}` : "暂无数据";
        if (data.unit) {
          price += ` / ${data.unit}`;
        }

        let item = { name, price: price as string, key: name };
        res.push(item);
      } catch (error) {
        console.log(error.toString());
        res.push({
          name: searchTerm,
          price: "获取失败，请稍后再试",
          key: searchTerm,
        });
      }
    }
    this.loading = false;
    this.displayData = res;
    this.lastUpdate = new Date().toLocaleString();
  }

  mounted(): void {
    this.$nextTick(() => {
      this.fetchData();
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
