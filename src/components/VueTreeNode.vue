<template>
  <div class="vue-tree__node">
    <slot>Default</slot>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({

});
</script>
<style lang="scss" scoped>
.vue-tree__node {
  border: 1px solid blue;
  padding: 16px;
}
</style>
