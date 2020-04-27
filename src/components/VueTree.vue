<template>
  <div
    ref="rootTree"
    class="tree-branch"
  >
    <div class="tree-branch__parent">
      <slot v-bind="data">
        {{ data.value }}
      </slot>
    </div>
    <div
      v-if="data.children && data.children.length"
      class="tree-branch__children"
    >
      <template
        v-for="child of data.children"
      >
        <vue-tree
          ref="subTreeComponents"
          v-slot:default="childData"
          :key="child.value"
          v-bind="$attrs"
          :data="child"
        >
          <slot v-bind="childData">
            {{ childData.value }}
          </slot>
        </vue-tree>
      </template>
    </div>
  </div>
</template>
<script lang="ts">
import Vue from 'vue';
import VueTreeLine from './VueTreeLine.vue';

export default Vue.extend({
  name: 'VueTree',
  inheritAttrs: false,
  props: {
    data: {
      type: Object,
      required: true,
    },
  },
  mounted() {
    this.drawLines();
  },
  methods: {
    drawLines() {
      if (!this.$refs.subTreeComponents || !(this.$refs.subTreeComponents as []).length) {
        return;
      }

      const { rootTree } = this.$refs;
      const subTrees = (this.$refs.subTreeComponents as Vue[]).map((s) => s.$el);

      const rootElem = (rootTree as HTMLElement).querySelector('.tree-branch__parent');
      if (!rootElem) { return; }

      subTrees.forEach((subTree) => {
        const subTreeElem = subTree.querySelector('.tree-branch__parent');
        if (!subTreeElem) { return; }

        this.drawLine(rootTree as HTMLElement, rootElem as HTMLElement, subTreeElem as HTMLElement);
      });
    },

    // For this function it is important that the sourceElement is the parent
    // and the destination element is the child. For the bind points will be the bottom center
    // at the parent, and the top center at the child.
    drawLine(
      containerElement: HTMLElement, sourceElement: HTMLElement, destinationElement: HTMLElement,
    ) {
      // Helper function to return the offset object between a parent
      // node and any of its (grand)children.
      const getOffset = (parent: HTMLElement, child: HTMLElement) => {
        let offsetLeft = 0;
        let offsetTop = 0;

        let currentChild = child;
        while (currentChild !== parent) {
          if (!currentChild) {
            break;
          }

          offsetLeft += currentChild.offsetLeft;
          offsetTop += currentChild.offsetTop;
          currentChild = currentChild.parentNode as HTMLElement;
        }

        return {
          offsetLeft, offsetTop, offsetHeight: child.offsetHeight, offsetWidth: child.offsetWidth,
        };
      };

      const destinationOffset = getOffset(containerElement, destinationElement);
      const sourceOffset = getOffset(containerElement, sourceElement);

      const ComponentClass = Vue.extend(VueTreeLine);
      const instance = new ComponentClass({
        propsData: {
          point1: {
            xOffset: destinationOffset.offsetLeft + destinationElement.offsetWidth / 2,
            yOffset: destinationOffset.offsetTop, // top of destination element
          },
          point2: {
            xOffset: sourceOffset.offsetLeft + sourceOffset.offsetWidth / 2,
            yOffset: sourceOffset.offsetTop + sourceOffset.offsetHeight, // bottom of source element
          },
          lineColor: 'blue',
          lineWidth: 2,
        },
      });

      instance.$mount();
      containerElement.appendChild(instance.$el);
    },
  },
});
</script>

<style lang="scss" scoped>
.tree-branch {
  // background-color: red;

  display: flex;
  flex-direction: column;

  align-items: center;
  position: relative;
}

.tree-branch__parent {
  position: relative;
  margin: 16px;
}
.tree-branch__children {
  display: flex;
  flex-direction: row;
  position: relative;
}

</style>
