<template>
  <div style="padding: 50px">
    <div class="grid grid-cols-2 gap-12 mb-12">
      <forcast-card></forcast-card>
      <real-time-card></real-time-card>
    </div>
    <compare-card></compare-card>
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
import ForcastCard from "../components/ForcastCard.vue";
import RealTimeCard from "../components/RealTimeCard.vue";
import CompareCard from "../components/CompareCard.vue";

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
    ForcastCard,
    RealTimeCard,
    CompareCard,
  },
})
export default class Dashboard extends Vue {}
</script>

<style scoped>
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
