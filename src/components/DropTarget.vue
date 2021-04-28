<template>
  <div
    :class="classes"
    @mouseenter="hover = true"
    @mouseleave="hover = false"
    @mouseup="onDrop"
  >
    {{ acceptsDescription }}
  </div>
</template>

<script>
import { computed, ref } from "vue";

export default {
  name: "DropTargets",
  props: {
    accepts: Array,
    index: Number,
    currentDragItem: Object,
  },
  emits: ["onDrop"],
  setup(props, { emit }) {
    const hover = ref(false);
    const active = () => {
      return (
        props.currentDragItem &&
        props.accepts.includes(props.currentDragItem.type)
      );
    };
    const disabled = () => {
      return (
        props.currentDragItem &&
        !props.accepts.includes(props.currentDragItem.type)
      );
    };
    const classes = computed(() => {
      return [
        "dnd-drop-target",
        props.accepts.join(" "),
        active() ? "active" : "",
        disabled() ? "disabled" : "",
        hover.value ? "hover" : "",
        active() && props.currentDragItem.type == "green" ? "active-green" : "",
        active() && props.currentDragItem.type == "blue" ? "active-blue" : "",
      ].join(" ");
    });
    const acceptsDescription = computed(() => {
      let desc = "accepts ";
      if (props.accepts.length > 0) {
        desc += props.accepts.join("&");
      } else {
        desc += "nothing";
      }
      return desc;
    });

    const onDrop = () => {
      if (active()) {
        emit("onDrop", { index: props.index });
      }
    };

    return { hover, classes, acceptsDescription, onDrop };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss"></style>
