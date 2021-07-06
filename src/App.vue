<template>
  <a-layout class="layout">
    <a-layout-header>
      <a-menu
        theme="dark"
        mode="horizontal"
        :style="{ lineHeight: '64px' }"
        :selected-keys="[selectedTab]"
        @click="switchTab"
      >
        <a-menu-item key="dashboard">造价信息数字化平台</a-menu-item>
        <a-menu-item key="forcast">价格预测</a-menu-item>
        <a-menu-item key="realtime">实时价格</a-menu-item>
      </a-menu>
    </a-layout-header>
    <a-layout-content style="padding: 0 50px">
      <dashboard v-if="selectedTab === 'dashboard'"></dashboard>
      <forcast v-if="selectedTab === 'forcast'"></forcast>
      <realTime v-if="selectedTab === 'realtime'"></realTime>
    </a-layout-content>
    <a-layout-footer style="text-align: center">
      ©2021 Wei Song. All Right Reserved.
    </a-layout-footer>
  </a-layout>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
import { Layout, Menu } from "ant-design-vue";
import Forcast from "./pages/Forcast.vue";
import RealTime from "./pages/RealTime.vue";
import Dashboard from "./pages/Dashboard.vue";

@Options({
  components: {
    ALayout: Layout,
    ALayoutHeader: Layout.Header,
    ALayoutContent: Layout.Content,
    ALayoutFooter: Layout.Footer,
    AMenu: Menu,
    AMenuItem: Menu.Item,
    Forcast,
    RealTime,
    Dashboard,
  },
})
export default class App extends Vue {
  searchTerm = "";
  records = [];

  selectedTab = "dashboard";

  switchTab({ key }: { key: string }): void {
    this.selectedTab = key;
  }
}
</script>
