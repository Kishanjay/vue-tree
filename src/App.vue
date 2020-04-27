<template>
  <div id="app">
    <vue-tree
      v-slot:default="node"
      :data="tree"
      :line-width="6"
      :node-spacing="24"
    >
      <div class="node">
        <h1>Node: {{ node.value }}</h1>
        <h2>subtitle</h2>
        <img
          src="http://placehold.it/64x64"
          alt=""
        >
      </div>
    </vue-tree>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import VueTree from './components/VueTree.vue';

export default Vue.extend({
  name: 'App',
  components: {
    VueTree,
  },
  data() {
    return {
      tree: {
        value: 'a',
        children: [
          {
            value: 'b',
            children: [
              { value: 'b' },
              { value: 'b' },
            ],
          },
        ],
      },
    };
  },
});
</script>

<style lang="scss">
#app {
  min-width: 100vw;
  min-height: 100vh;
  position: relative;
}

.node {
  padding: 16px;
  border: 5px solid black;

  border-radius: 4px;
}
</style>
