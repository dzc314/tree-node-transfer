<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" @click="showData"/>
    <TreeNodeTransfer :source="source" :target="target" />
  </div>
</template>

<script>
import TreeNodeTransfer from "./components/TreeNodeTransfer.vue";

export default {
  name: "App",
  components: {
    TreeNodeTransfer,
  },
  data() {
    return {
      source: [
        {
          code: 1,
          parendCode: null,
          isLeaf:false,
          name: "一级",
          children: [
            {
              code: 2,
              parendCode: 1,
              isLeaf:false,
              name: "二级1",
              children: [
                {
                  code: 3,
                  parendCode: 2,
                  isLeaf:false,
                  name: "三级1.1",
                  children: []
                },
                {
                  code: 4,
                  parendCode: 2,
                  isLeaf:false,
                  name: "三级1.2",
                },
              ],
            },
            {
              code: 5,
              parendCode: 1,
              isLeaf:false,
              name: "二级2",
              children: [
                {
                  code: 6,
                  parendCode: 2,
                  isLeaf:false,
                  name: "三级2.1",
                },
                {
                  code: 7,
                  parendCode: 2,
                  isLeaf:false,
                  name: "三级2.2",
                },
              ],
            },
            {
              code: 8,
              parendCode: 1,
              isLeaf:false,
              name: "二级3",
              children: []
            },
          ],
        },
      ],
      target: [
        {
          code: 8,
          parendCode: 1,
          isLeaf:false,
          name: "二级3",
        },
      ],
    };
  },
  methods: {
    showData() {
      console.log(this.target);
    }
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
