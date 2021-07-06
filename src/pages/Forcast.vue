<template>
  <div style="padding: 50px">
    <h2>价格预测</h2>
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
    <a-empty v-if="!error && !loading && records.length === 0" />

    <div v-if="!error && !loading && records.length > 0">
      <a-collapse>
        <a-collapse-panel
          v-for="recordIndex in currentRecords"
          :set="(record = records[recordIndex])"
          :key="recordIndex"
          :header="record.name + (record.type ? '(' + record.type + ')' : '')"
        >
          <record-card
            :name="record.name"
            :id="record.id"
            :type="record.type"
            :unit="record.unit"
            :prices="record.prices"
          ></record-card>
        </a-collapse-panel>
      </a-collapse>

      <a-pagination
        v-if="records.length > 10"
        style="margin-top: 30px"
        :total="records.length"
        :page-size="10"
        v-model:current="currentPage"
      />
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
  Pagination,
} from "ant-design-vue";
import RecordCard from "../components/RecordCard.vue";
import axios from "axios";

@Options({
  components: {
    AButton: Button,
    AInputSearch: Input.Search,
    ACollapse: Collapse,
    ACollapsePanel: Collapse.Panel,
    Alert,
    RecordCard,
    AEmpty: Empty,
    ASpin: Spin,
    APagination: Pagination,
  },
})
export default class App extends Vue {
  searchTerm = "";
  records = [];
  loading = false;
  error = "";

  currentPage = 1;

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
    this.records = [];
    this.currentPage = 1;
    try {
      const { data } = await axios.get("api/records", {
        params: { search: this.searchTerm },
      });
      this.records = data;
    } catch (error) {
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
