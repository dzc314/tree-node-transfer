<template>
  <el-row class="transfer">
    <el-col :span="10" class="transfer-view">
      <div class="title">
        <h3>{{title[0]}}</h3>
      </div>
      <el-tree
        class="tree-view"
        ref="sourceTree"
        :data="leftData"
        :props="props"
        :node-key="nodeKey"
        show-checkbox
        default-expand-all
      >
      </el-tree>
    </el-col>
    <el-col :span="4" class="transfer-handle">
      <el-button icon="el-icon-d-arrow-right" @click="transRight"></el-button>
      <el-button icon="el-icon-d-arrow-left" @click="transLeft"></el-button>
    </el-col>
    <el-col :span="10" class="transfer-view">
      <div class="title">
        <h3>{{title[1]}}</h3>
      </div>
      <el-tree
        class="tree-view"
        ref="targetTree"
        :data="rightData"
        :props="props"
        :node-key="nodeKey"
        show-checkbox
        default-expand-all
      >
      </el-tree>
    </el-col>
  </el-row>
</template>

<script>
import { cloneDeep } from 'lodash'
export default {
  name: 'EleTreeTransfer',
  props: {
    source: {
      type: Array,
      default: () => [],
    },
    target: {
      type: Array,
      default: () => [],
    },
    sourceIsArray: {
      type: Boolean,
      default: false,
    },
    targetIsArray: {
      type: Boolean,
      default: false,
    },
    nodeKey: {
      type: String,
      default: 'id',
    },
    parentKey: {
      type: String,
      default: 'parentId',
    },
    title: {
      type: Array,
      default: () => ['源数据', '目标数据']
    },
    props: {
      type: Object,
      default: () => {
        return {
          label: 'name', //指定节点标签为节点对象的某个属性值
          // children: 'children', //指定子树为节点对象的某个属性值
        }
      },
    },
  },
  data() {
    return {
      leftData: [],
      rightData: [],
    }
  },
  model: {
    prop: 'target',
    event: 'change',
  },
  watch: {
    source: {
      handler: function (newVal) {
        if (newVal && newVal.length) {
          this.leftInit()
        }
      },
    },
    target: {
      handler: function (newVal) {
        if (newVal && newVal.length) {
          // console.log(newVal);
          this.rightInit()
        }
      },
    },
  },
  mounted() {},
  methods: {
    leftInit() {
      if (this.sourceIsArray) {
        this.leftData = this.arrayToTree(
          this.source,
          this.nodeKey,
          this.parentKey
        )
      } else {
        this.leftData = cloneDeep(this.source)
      }
      this.buildSourceTree()
    },
    rightInit() {
      if (this.targetIsArray) {
        this.rightData = this.arrayToTree(
          this.target,
          this.nodeKey,
          this.parentKey
        )
      } else {
        this.rightData = cloneDeep(this.target)
      }
      this.buildSourceTree()
    },
    buildSourceTree() {
      let transData = []
      if (this.targetIsArray) {
        transData = cloneDeep(this.target)
      } else {
        transData = this.treeToArray(this.target)
      }

      this.leftData = this.treeRemoveItems(
        this.leftData,
        transData,
        this.nodeKey
      )
    },
    cloneDeep(obj) {
      return JSON.parse(JSON.stringify(obj))
    },
    arrayToTree(arr, nodeKey, parentKey) {
      if (!Array.isArray(arr) || !arr.length) {
        return []
      }
      let map = {}
      arr.forEach((item) => {
        const key = item[nodeKey]
        map[key] = item
      })
      let tree = []
      arr.forEach((item) => {
        const parentId = item[parentKey]
        const parent = map[parentId]
        if (parent) {
          if (!parent.children) {
            parent.children = []
          }
          parent.children.push(item)
        } else {
          tree.push(item)
        }
      })
      return tree
    },
    treeToArray(tree) {
      const result = []
      function getNodes(arr) {
        arr.forEach((item) => {
          const { children, ...node } = item
          result.push(node)
          if (children && children.length) {
            getNodes(children)
          }
        })
      }
      getNodes(tree)
      return result
    },
    treeRemoveItems(tree, items, nodeKey) {
      function filterChildren(tree) {
        tree.forEach((node) => {
          const inArr = items.some((item) => item[nodeKey] === node[nodeKey])
          if (!inArr) {
            return
          }
          if (node.children && node.children.length) {
            node.children = filterChildren(node.children)
          }
        })
        const result = tree.filter((node) => {
          const inArr = items.some((item) => item[nodeKey] === node[nodeKey])
          return !inArr || (inArr && node.children && node.children.length)
        })
        return result
      }
      return filterChildren(tree)
    },
    treeMergeItems(tree, items, parentId) {
      if (!items.length) {
        return
      }
      items.forEach((item) => {
        if (item[this.parentKey] === parentId) {
          const itemInTree = tree.some(
            (node) => node[this.nodeKey] === item[this.nodeKey]
          )
          if (!itemInTree) {
            item.children = []
            tree.push(item)
          }
        }
      })
      tree.forEach((child) => {
        if (child.children) {
          this.treeMergeItems(child.children, items, child[this.nodeKey])
        }
      })
    },
    transRight() {
      const data = this.$refs.sourceTree.getCheckedNodes(false, true)
      const transData = cloneDeep(data)
      this.treeMergeItems(this.rightData, transData, null)
      this.leftData = this.treeRemoveItems(
        this.leftData,
        transData,
        this.nodeKey
      )
      this.setModelValue()
      // this.$emit('change', 'this.treeToArray(this.rightData)')
    },
    transLeft() {
      const data = this.$refs.targetTree.getCheckedNodes(false, true)
      // console.log(data)
      const transData = cloneDeep(data)
      this.treeMergeItems(this.leftData, transData, null)
      this.rightData = this.treeRemoveItems(
        this.rightData,
        transData,
        this.nodeKey
      )
      this.setModelValue()
      // this.$emit('change', this.rightData)
    },
    setModelValue() {
      let data = []
      if (this.targetIsArray) {
        data = this.treeToArray(this.rightData)
      } else {
        data = cloneDeep(this.rightData)
      }
      const length = this.target.length
      Array.prototype.splice.apply(this.target, [0, length, ...data])
    },
  },
}
</script>

<style lang="scss" scoped>
.transfer {
  height: 100%;
  min-height: 200px;
  > div {
    height: 100%;
  }
  .transfer-view {
    display: flex;
    flex-direction: column;
    border: 1px solid #ddd;
    > .title {
      border-bottom: 1px solid #ddd;
      h3 {
        margin: 0;
      }
    }
    > .tree-view {
      flex: 1;
      overflow: auto;
    }
  }
  .transfer-handle {
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
</style>