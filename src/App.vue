<template>
  <div :class="classes">
    <div class="dnd-source-objects">
      <Draggable
        :class="'dnd-source-object green'"
        :type="green"
        :index="1"
        :children="1"
        @onDragStart="onDragStart"
        @onDragStop="onDragStop"
      />
    </div>
  </div>
</template>

<script>
import { computed, provide, reactive, readonly, ref } from "vue";
import Draggable from "./components/Draggable.vue";

export default {
  name: "App",
  components: {
    Draggable,
  },
  setup() {
    const currentDragItem = ref(null);
    const dragging = reactive({
      value: false,
    });
    provide("dragging", readonly(dragging));
    const state = ref({
      originX: 0,
      originY: 0,
      elementX: 0,
      elementY: 0,
    });

    const updateState = (s) => {
      state.value = { ...s };
    };
    provide("updateState", updateState);

    const left = reactive({
      value: 0,
    });
    const top = reactive({
      value: 0,
    });
    provide("left", readonly(left));
    provide("top", readonly(top));
    let onDragStart = (details) => {
      currentDragItem.value = details;
    };

    const onMouseMove = (event) => {
      let deltaX = event.pageX - state.value.originX;
      let deltaY = event.pageY - state.value.originY;
      let distance = Math.abs(deltaX) + Math.abs(deltaY);

      if (!dragging.value && distance > 3) {
        dragging.value = true;
        onDragStart(true);
      }

      if (dragging.value) {
        left.value = state.value.elementX + deltaX + document.body.scrollLeft;
        top.value = state.value.elementY + deltaY + document.body.scrollTop;
      }
    };

    const onMouseUp = () => {
      document.removeEventListener("mousemove", onMouseMove);
      document.removeEventListener("mouseup", onMouseUp);
      dragging.value = false;
      currentDragItem.value = null;
    };

    provide("onMouseMove", onMouseMove);
    provide("onMouseUp", onMouseUp);

    const classes = computed(() => {
      let c = "dnd-example";
      if (currentDragItem.value) {
        c += " dragging";
      }
      return c;
    });

    const onDragStop = () => {
      currentDragItem.value = null;
    };

    return {
      classes,
      onDragStart,
      onDragStop,
    };
  },
};
</script>

<style lang="sass">
$green: #B0DE6B
$blue: #76C9DE
$dark-gray: #948F8F
$light-gray: #ddd

body
  background: $light-gray

*
  box-sizing: border-box

.dnd-example
  display: flex
  flex-wrap: wrap
  width: 740px
  padding: 30px
  margin: 100px auto 0
  background: white
  font-family: sans-serif
  font-size: 19px
  text-align: center

  &,
  div
    border-radius: 10px
    user-select: none

  &.dragging *
    cursor: grabbing

.drop-description
  width: 100%
  font-weight: 300
  margin: 1em 0 0

.dnd-source-objects
  margin-right: 30px

.dnd-source-object
  width: 240px
  line-height: 38px
  border-radius: 10px
  font-weight: bold
  font-size: 24px
  color: rgba(0,0,0,.2)
  cursor: grab

  &:not(:last-child)
    margin: 0 0 10px

  &.green
    background: $green

  &.blue
    background: $blue

.dnd-drop-targets
  background: $dark-gray
  flex: 1
  padding: 15px

.dnd-drop-target
  position: relative
  background: white
  line-height: 50px
  font-weight: 300
  overflow: hidden

  &:not(:last-child)
    margin: 0 0 10px

  &.green:before,
  &.blue:after
    content: ''
    position: absolute
    left: 0
    width: 20px
    height: 100%
    background: $green

  &.blue:after
    background: $blue

  &.green.blue:after
    left: 20px

  &.active.hover
    &:after,
    &:before
      display: none

  &.active-green.active
    box-shadow: 0 0 0 3px $dark-gray, 0 0 0 4px $green
    &.hover
      background: $green

  &.active-blue.active
    box-shadow: 0 0 0 3px $dark-gray, 0 0 0 4px $blue
    &.hover
      background: $blue

  &.disabled
    opacity: .2
    cursor: no-drop

.dnd-draggable.dragging
  z-index: 1
  pointer-events: none
  box-shadow: 0 2px 5px rgba(0,0,0,.8)
</style>
