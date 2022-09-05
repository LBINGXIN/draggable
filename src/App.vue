<template>
  <div class="page">
    <div class="page-toolvbar">
      <el-button type="primary" @click="setMultiTop" size="small"
        >置顶</el-button
      >
      <el-button type="primary" @click="setMultiBottom" size="small"
        >置底</el-button
      >
    </div>
    <Draggable
      ref="drag"
      v-model="list"
      :columns="columns"
      @change="handleData"
    ></Draggable>
  </div>
</template>
<script>
import Draggable from "./components/Draggable.vue";
export default {
  components: {
    Draggable,
  },
  data() {
    return {
      // 显示字段
      columns: [
        { prop: "order", label: "排序" },
        { prop: "name", label: "名称" },
        { prop: "age", label: "年龄" },
      ],
      // 数据
      list: [
        { id: "1", order: 1, name: "小明1", age: 18 },
        { id: "2", order: 2, name: "小明2", age: 28 },
        { id: "3", order: 3, name: "小明3", age: 33 },
        { id: "4", order: 4, name: "小明4", age: 44 },
        { id: "5", order: 5, name: "小明5", age: 55 },
        { id: "6", order: 6, name: "小明6", age: 66 },
      ],
    };
  },
  methods: {
    // change 事件
    handleData(list) {
      console.log(list);
    },
    // 置顶
    setMultiTop() {
      this.$refs.drag.setMultiTop();
    },
    // 置底
    setMultiBottom() {
      this.$refs.drag.setMultiBottom();
    },
  },
};
</script>
<style lang="scss">
.page {
  padding: 25px;
  .page-toolbar {
    line-height: 80px;
  }
}
</style>
