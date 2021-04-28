<template>
  <div
    :class="classes"
    :style="style"
    @mousedown="onMouseDown"
    @mouseup="onMouseUp"
  >
    {{ children }}
  </div>
</template>

<script>
import { computed, inject } from "vue";

export default {
  name: "Draggable",
  props: {
    type: String,
    index: Number,
    children: Number,
  },
  setup(props) {
    const dragging = inject("dragging");
    const left = inject("left");
    const top = inject("top");
    const dragData = inject("dragData");
    const updatedragData = inject("updatedragData");
    const onMouseMove = inject("onMouseMove");
    const onMouseUp = inject("onMouseUp");
    const classes = computed(() => {
      let c = "dnd-draggable";
      if (dragging.value && dragData?.value.index == props.index) {
        c += " dragging";
      }
      return c;
    });
    const style = computed(() => {
      if (dragging.value && dragData?.value.index == props.index) {
        return {
          position: "absolute",
          left: left.value + "px",
          top: top.value + "px",
        };
      } else {
        return {};
      }
    });

    const updateState = inject("updateState");
    const onMouseDown = (event) => {
      if (event.button === 0) {
        event.stopPropagation();

        let pageOffset = event.target.getBoundingClientRect();

        updateState({
          originX: event.pageX,
          originY: event.pageY,
          elementX: pageOffset.left,
          elementY: pageOffset.top,
        });

        updatedragData({ type: props.type, index: props.index });

        document.addEventListener("mousemove", onMouseMove);
        document.addEventListener("mouseup", onMouseUp);
      }
    };

    return { classes, style, onMouseDown, onMouseUp, dragging };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss"></style>
