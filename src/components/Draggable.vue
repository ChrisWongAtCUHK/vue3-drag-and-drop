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
import { computed, inject, ref } from "vue";

export default {
  name: "Draggable",
  props: {
    type: String,
    index: Number,
    children: Number,
  },
  setup(props) {
    const originX = ref(0);
    const originY = ref(0);
    const elementX = ref(0);
    const elementY = ref(0);

    const dragging = ref(false);
    const left = ref("left");
    const top = ref("top");
    const dragData = inject("dragData");
    const updatedragData = inject("updatedragData");
    const onDragStart = inject("onDragStart");
    const onDragStop = inject("onDragStop");
    const classes = computed(() => {
      let c = "dnd-draggable";
      if (dragging.value) {
        c += " dragging";
      }
      return c;
    });
    const style = computed(() => {
      if (dragging.value) {
        return {
          position: "absolute",
          left: left.value + "px",
          top: top.value + "px",
        };
      } else {
        return {};
      }
    });

    const onMouseMove = (event) => {
      let deltaX = event.pageX - originX.value;
      let deltaY = event.pageY - originY.value;
      let distance = Math.abs(deltaX) + Math.abs(deltaY);

      if (!dragging.value && distance > 3) {
        dragging.value = true;
        onDragStart(dragData);
      }

      if (dragging.value) {
        left.value = elementX.value + deltaX + document.body.scrollLeft;
        top.value = elementY.value + deltaY + document.body.scrollTop;
      }
    };

    const onMouseUp = () => {
      document.removeEventListener("mousemove", onMouseMove);
      document.removeEventListener("mouseup", onMouseUp);
      dragging.value = false;
      onDragStop();
    };

    const onMouseDown = (event) => {
      if (event.button === 0) {
        event.stopPropagation();

        let pageOffset = event.target.getBoundingClientRect();

        originX.value = event.pageX;
        originY.value = event.pageY;
        elementX.value = pageOffset.left;
        elementY.value = pageOffset.top;

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
