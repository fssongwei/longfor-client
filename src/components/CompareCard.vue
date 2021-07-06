<template>
  <a-card title="主要材料信息价涨跌情况" :hoverable="true">
    <a-row :gutter="16">
      <a-col :span="8">
        <detail-panel></detail-panel>
      </a-col>
      <a-col :span="8">
        <detail-panel></detail-panel>
      </a-col>
      <a-col :span="8">
        <detail-panel></detail-panel>
      </a-col>
    </a-row>
  </a-card>
</template>

<script lang="ts">
import { Options, Vue } from "vue-property-decorator";
import { Card, Row, Col } from "ant-design-vue";
import { SyncOutlined } from "@ant-design/icons-vue";
import DetailPanel from "./DetailPanel.vue";

@Options({
  components: {
    ACard: Card,
    SyncOutlined,
    ARow: Row,
    ACol: Col,
    DetailPanel,
  },
})
export default class CompareCard extends Vue {}
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
