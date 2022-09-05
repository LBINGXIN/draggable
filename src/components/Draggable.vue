<template>
  <div class="draggable">
    <el-table
      ref="table"
      :data="tableData"
      stripe
      style="width: 100%"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column
        :prop="item.prop"
        :label="item.label"
        v-for="item in columns"
        :key="item.prop"
      >
      </el-table-column>
      <el-table-column width="200" label="操作" align="center">
        <template #default="{ $index }">
          <div class="list">
            <div class="list-item handle" title="拖动">
              <img src="./icons/move.png" alt="" />
            </div>
            <div class="list-item" @click.stop="moveUp($index)" title="上移">
              <img src="./icons/moveUp.png" alt="" />
            </div>
            <div class="list-item" @click.stop="moveDown($index)" title="下移">
              <img src="./icons/moveDown.png" alt="" />
            </div>
            <div class="list-item" @click.stop="setBottom($index)" title="置底">
              <img src="./icons/setBottom.png" alt="" />
            </div>
            <div class="list-item" @click.stop="setTop($index)" title="置顶">
              <img src="./icons/setTop.png" alt="" />
            </div>
          </div>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { Sortable, MultiDrag } from "sortablejs";

Sortable.mount(new MultiDrag());
export default {
  name: "Draggable",
  model: {
    prop: "tableData",
    event: "change",
  },
  props: {
    columns: {
      type: Array,
      default: () => [],
    },
    tableData: {
      type: Array,
      default: () => [],
    },
    field: {
      type: String,
      default: "id",
    },
  },
  data() {
    return {
      selectList: [],
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      const ele = document.querySelector(".draggable tbody");
      // eslint-disable-next-line no-unused-vars
      const sortable = new Sortable(ele, {
        // handle: '.ec-draggable-handle',
        multiDrag: true,
        selectedClass: "selected",
        animation: 150,
        draggable: ".el-table__row",
        ghostClass: "draggable-ghost",
        onEnd: ({ oldIndex, newIndex, oldIndicies }) => {
          const { tableData, handleDiff } = this;
          if (oldIndicies.length > 0) {
            const selectList = oldIndicies.map((item) => {
              return tableData[item.index];
            });
            const list = handleDiff(tableData, selectList);
            list.splice(newIndex, 0, ...selectList);
            this.handleEmit(list, true);
          } else {
            const item = tableData.splice(oldIndex, 1)[0];
            tableData.splice(newIndex, 0, item);
            this.handleEmit(tableData, true);
          }
        },
      });
    },
    handleSelectionChange(val) {
      this.selectList = val;
    },
    handleSelect() {
      return this.selectList;
    },
    // 判断是否选中行
    hasSelectRow() {
      const list = this.handleSelect();
      if (!list.length) {
        this.$message.error("请先选择行！");
      }
      return list.length > 0;
    },
    // emit
    handleEmit(tableData) {
      tableData.forEach((item, index) => {
        item.order = index + 1;
      });
      this.$emit("change", tableData);
    },
    // 处理差集
    handleDiff(list, selectList) {
      return [...list].filter((x) =>
        [...selectList].every((y) => y.id !== x.id)
      );
    },
    // 置顶
    setMultiTop() {
      // 未选择行
      if (!this.hasSelectRow()) return;
      const { tableData } = this;
      const selectList = this.handleSelect();
      const diffList = this.handleDiff(tableData, selectList);
      this.handleEmit([...selectList, ...diffList]);
    },
    // 置底
    setMultiBottom() {
      // 未选择行
      if (!this.hasSelectRow()) return;
      const { tableData } = this;
      const selectList = this.handleSelect();
      const diffList = this.handleDiff(tableData, selectList);
      this.handleEmit([...diffList, ...selectList]);
    },
    // 置顶
    setTop(index) {
      // 第一行，不需要改变
      if (index === 0) return;
      const { tableData } = this;
      tableData.unshift(tableData.splice(index, 1)[0]);
      this.handleEmit(tableData);
    },
    // 置底
    setBottom(index) {
      const { tableData } = this;
      // 最后一行，不需要改变
      if (index === tableData.length - 1) return;
      tableData.push(tableData.splice(index, 1)[0]);
      this.handleEmit(tableData);
    },
    // 上移
    moveUp(index) {
      // 第一行，不需要改变
      if (index === 0) return;
      const { tableData } = this;
      tableData[index] = tableData.splice(index - 1, 1, tableData[index])[0];
      this.handleEmit(tableData);
    },
    // 下移
    moveDown(index) {
      const { tableData } = this;
      // 最后一行，不需要改变
      if (index === tableData.length - 1) return;
      tableData[index] = tableData.splice(index + 1, 1, tableData[index])[0];
      this.handleEmit(tableData);
    },
  },
};
</script>
<style lang="scss">
.draggable {
  .list {
    display: flex;
    width: 100%;
    height: 100%;
    &-item {
      flex: 1;
      display: flex;
      justify-content: center;
      align-items: center;
      img {
        width: 20px;
        height: 20px;
        margin: 0 10px;
        cursor: pointer;
      }
    }
  }
}
.draggable-ghost {
  background: skyblue;
}
</style>
