<template>
  <div :class="classes" @mouseenter="hover = true" @mouseleave="hover = false">
    {{ acceptsDescription }}
  </div>
</template>

<script>
import { computed, ref } from "vue";

export default {
  name: "DropTargets",
  props: {
    accepts: Array,
    currentDragItem: Object,
  },
  setup(props) {
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

    return { hover, classes, acceptsDescription };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss"></style>
