<template>
  <el-row :gutter="20">
    <el-col :span="12">
      <el-tree
        ref="tree"
        :data="source"
        :props="props"
        node-key="code"
        show-checkbox
        default-expand-all
        :filter-node-method="filterNode"
        @check="handleCheck"
      >
        <div class="custom-tree-node" slot-scope="{ node, data }">
          <span>{{ data.name }}</span>
          <i
            v-if="!data.isLeaf && !data.children"
            class="el-icon-caret-bottom"
            @click="loadNodeChildren(node, data)"
          ></i>
        </div>
      </el-tree>
    </el-col>
    <el-col :span="12">
      <ul>
        <li v-for="(item, index) in target" :key="index">
          <span>{{ item.name }}</span
          ><i class="el-icon-circle-close" @click="targetHandle(index)"></i>
        </li>
      </ul>
    </el-col>
  </el-row>
</template>

<script>
export default {
  name: "TreeNodeTransfer",
  props: {
    source: {
      type: Array,
      default: () => [],
    },
    target: {
      type: Array,
      default: () => [],
    },
  },
  model: {
    prop: "target",
    event: "change",
  },
  data() {
    return {
      props: {
        label: "name",
      },
    };
  },
  watch: {
    target: {
      handler: function (val) {
        this.$refs.tree.filter(val);
      },
      deep: true,
    },
  },

  mounted() {
    this.renderSourceTree(this.source);
    this.$refs.tree.filter(null);
  },
  methods: {
    renderSourceTree(nodes) {
      if (!nodes.length) {
        return;
      }
      const target = this.target;
      nodes.forEach((node) => {
        let nodeSelected = false;
        if (!node.isLeaf && !node.children) {
          this.$set(node, "disabled", true);
        } else {
          this.$set(node, "disabled", false);
        }
        if (node.children && node.children.length) {
          this.renderSourceTree(node.children);
          const isChildrenAllSelected = node.children.every(
            (child) => !child.visible
          );
          nodeSelected = isChildrenAllSelected;
        } else {
          nodeSelected = target.some((item) => {
            return item.code === node.code;
          });
        }
        node.visible = !nodeSelected;
      });
    },

    sourceToTarget(data) {
      if (data.children && data.children.length) {
        data.children.forEach((child) => {
          this.sourceToTarget(child);
        });
      } else {
        this.target.push(data);
      }
    },
    handleCheck(data) {
      // console.log(data);
      data.visible = false;
      this.sourceToTarget(data);
      this.renderSourceTree(this.source);
      this.$refs.tree.setCheckedKeys([]);
    },
    filterNode(value, data) {
      return data.visible;
    },
    targetHandle(index) {
      this.target.splice(index, 1);
      this.renderSourceTree(this.source);
    },
    loadNodeChildren(node, data) {
      if (!data.children) {
        this.$set(data, "children", []);
      }
      //   模拟请求数据
      setTimeout(() => {
        data.children = [
          {
            name: "test",
            code: Math.round(Math.random() * 10000),
            isLeaf: Math.random() > 0.5,
          },
          {
            name: "test",
            code: Math.round(Math.random() * 10000),
            isLeaf: Math.random() > 0.5,
          },
        ];
        // 重新渲染树以匹配已选择数据
        this.renderSourceTree(this.source);
        // 默认全部展开则不需要设置节点展开
        // node.expanded = true;
      }, 1000);
    },
  },
};
</script>

<style>
</style>