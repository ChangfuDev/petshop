<template>
  <div>
    <span class="title">消费记录</span>
    <el-tabs value="first" class="page">
      <el-tab-pane label="购物" name="first">
        <buy></buy>
      </el-tab-pane>
      <el-tab-pane label="寄养" name="second">
        <care></care>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>

<script>
import buy from "./order/buy.vue";
import care from "./order/care.vue";
export default {
  components: {
    care,
    buy
  },
  data() {
    return {};
  },
  methods: {}
};
</script>
<style scoped>
.title {
  font-size: 25px;
}

.page {
  margin-top: 20px;
}
</style>