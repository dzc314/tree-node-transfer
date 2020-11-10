<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" @click="showData" />
    <!-- <TreeNodeTransfer :source="source" :target="target" /> -->
    <EleTreeTransfer
      ref="treeTransfer"
      :source="source"
      :target="target"
      target-is-array
      class="transfer"
    />
  </div>
</template>

<script>
// import EleTreeTransfer from './components/EleTreeTransfer.vue'
import '@dzc314/ele-tree-transfer'
import '@dzc314/ele-tree-transfer/dist/ele-tree-transfer.css'
export default {
  name: 'App',
  components: {
    // EleTreeTransfer,
  },
  data() {
    return {
      source: [],
      target: [],
    }
  },
  mounted() {
    setTimeout(() => {
      this.source = [
        {
          id: 1,
          parentId: null,
          name: '一级',
          children: [
            {
              id: 2,
              parentId: 1,
              name: '二级1',
              children: [
                {
                  id: 3,
                  parentId: 2,
                  name: '三级1.1',
                  children: [],
                },
                {
                  id: 4,
                  parentId: 2,
                  name: '三级1.2',
                },
              ],
            },
            {
              id: 5,
              parentId: 1,
              name: '二级2',
              children: [
                {
                  id: 6,
                  parentId: 5,
                  name: '三级2.1',
                },
                {
                  id: 7,
                  parentId: 5,
                  name: '三级2.2',
                },
              ],
            },
            {
              id: 8,
              parentId: 1,
              name: '二级3',
              children: [],
            },
          ],
        },
      ]
      
    }, 1000)
    setTimeout(() => {
      this.target = [
        { id: 1, parentId: null, name: '一级' },
        { id: 2, parentId: 1, name: '二级1' },
        { id: 4, parentId: 2, name: '三级1.2' },
        { id: 44, parentId: 22, name: '三级1.2' },
      ]
    }, 500);
  },
  methods: {
    showData() {
      console.log(this.target)
      console.log(this.$refs.treeTransfer.rightData);
    },
  },
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  .transfer {
    height: 100px;
  }
}
</style>
