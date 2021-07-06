<template>
  <div style="padding: 50px">
    <h2>实时价格搜索</h2>
    <div style="margin-bottom: 30px">
      <a-input-search
        v-model:value="searchTerm"
        placeholder="材料名"
        enter-button
        size="large"
        @search="onSearch"
      />
    </div>

    <div class="center">
      <a-spin v-if="loading" size="large" />
    </div>
    <alert v-if="error" :message="error" type="error" />
    <a-empty v-if="!error && !loading && price === ''" />

    <div v-if="!error && !loading && price !== ''" style="display: flex">
      <img
        v-if="image"
        :src="image"
        :alt="searchTerm"
        style="width: 300px; height: 300px; margin-right: 30px"
      />
      <div>
        <statistic title="实时价格" :value="displayPrice" />
        (更新时间: {{ new Date().toLocaleString() }})
        <p style="margin-top: 20px">
          共 {{ sources.length }} 个数据来源: <br />
          <span v-for="(src, index) in sources" :key="index">
            <a :href="src">{{ index }}</a
            >,
          </span>
        </p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
import {
  Button,
  Input,
  Collapse,
  Alert,
  Empty,
  Spin,
  Statistic,
} from "ant-design-vue";
import axios from "axios";

@Options({
  components: {
    AButton: Button,
    AInputSearch: Input.Search,
    ACollapse: Collapse,
    ACollapsePanel: Collapse.Panel,
    Alert,
    AEmpty: Empty,
    ASpin: Spin,
    Statistic,
  },
})
export default class RealTime extends Vue {
  searchTerm = "";
  records = [];
  loading = false;
  price = "";
  unit = "";
  image = "";
  sources = [];
  error = "";

  currentPage = 1;

  get displayPrice(): string {
    if (this.loading) return "正在获取实时价格 ...";
    if (this.price === "" || !this.price) return "暂无数据";
    let str = `￥${this.price}`;
    if (this.unit) {
      str += ` / ${this.unit}`;
    }
    return str;
  }

  get currentRecords(): Array<number> {
    let start = (this.currentPage - 1) * 10 + 1;
    let end = Math.min(this.records.length, start + 10 - 1);
    let arr = [];
    for (let i = start; i < end; i++) {
      arr.push(i);
    }
    return arr;
  }

  async onSearch(): Promise<void> {
    this.loading = true;
    this.error = "";
    this.currentPage = 1;

    this.price = "";
    this.unit = "";
    this.image = "";
    this.sources = [];

    try {
      const { data } = await axios.get("api/realTimePrice", {
        params: { search: this.searchTerm },
      });

      this.price = data.price;
      this.unit = data.unit;
      this.image = data.image;
      this.sources = data.sources;
    } catch (error) {
      console.log(error);
      this.error = `出现了一点小问题（${error.toString()}）`;
    }
    this.loading = false;
  }
}
</script>

<style scoped>
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
