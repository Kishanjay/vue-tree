<template>
  <div>
    <div
      class="tree-branch__line"
      :class="{'--child_right': childRight}"
      :style="styleObject"
    />

    <template v-if="showConnectors">
      <div
        class="dot"
        :style="{'left': point1.xOffset + 'px', 'top': point1.yOffset + 'px'}"
      />
      <div
        class="dot"
        :style="{'left': point2.xOffset + 'px', 'top': point2.yOffset + 'px'}"
      />
    </template>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
  props: {
    point1: {
      type: Object,
      required: true,
    },
    point2: {
      type: Object,
      required: true,
    },
    lineWidth: {
      type: Number,
      required: false,
      default() {
        return 2;
      },
    },
    lineColor: {
      type: String,
      required: false,
      default() {
        return 'black';
      },
    },
    showConnectors: {
      type: Boolean,
      required: false,
      default() {
        return false;
      },
    },
  },
  data() {
    const child = this.point1.yOffset < this.point2.yOffset ? this.point2 : this.point1;
    const parent = this.point1.yOffset < this.point2.yOffset ? this.point1 : this.point2;

    let childRight = false;
    if (child.xOffset > parent.xOffset) {
      childRight = true;
    }

    const xStart = this.point1.xOffset < this.point2.xOffset
      ? this.point1.xOffset
      : this.point2.xOffset;
    const yStart = this.point1.yOffset < this.point2.yOffset
      ? this.point1.yOffset
      : this.point2.yOffset;

    const horizontalLength = Math.abs(this.point1.xOffset - this.point2.xOffset);
    const verticalLength = Math.abs(this.point1.yOffset - this.point2.yOffset) / 2;

    return {
      childRight,
      styleObject: {
        '--ymid-coord': `${yStart + verticalLength - this.lineWidth / 2}px`,
        '--xstart-coord': `${xStart - this.lineWidth / 2 + 1}px`,
        '--horizontalLength': `${horizontalLength + this.lineWidth}px`,
        '--verticalLength': `${verticalLength + this.lineWidth / 2}px`,
        '--lineWidth': `${this.lineWidth}px`,
        '--lineColor': this.lineColor,
      },
    };
  },
});
</script>
<style lang="scss" scoped>
.tree-branch__line { // horizontal line
  position: absolute;
  top: var(--ymid-coord);
  left: var(--xstart-coord);
  width: var(--horizontalLength);
  height: var(--lineWidth);

  background-color: var(--lineColor);
  &::before, &::after { // vertical line base
    content: ' ';
    position: absolute;
    height: var(--verticalLength);
    width: var(--lineWidth);

    background-color: var(--lineColor);
  }

  &::before { // vertical line from child to mid
    top: 0px;
    left: 0px; // should be left or right (based on where the child is)
  }

  &::after { // vertical line from mid to parent
    bottom: 0px;
    right: 0px; // should be left or right (based on where the parent is)
  }

  &.--child_right {
    &::before {
      right: 0px;
      left: initial;
    }
    &::after {
      left: 0px;
      right: initial;
    }
  }
}

.dot {
  position: absolute;
  content: ' ';
  display: block;
  width: 10px;
  height: 10px;
  border: 2px solid black;
  border-radius: 100%;
  // TODO add transform based on width
}
</style>
