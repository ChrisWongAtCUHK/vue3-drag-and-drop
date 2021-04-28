<template>
  <div :class="classes">
    <div class="dnd-source-objects">
      <Draggable
        v-for="(source, index) in sources"
        :key="index"
        :class="`dnd-source-object ${source.type}`"
        :type="source.type"
        :index="index"
        :children="index"
      />
    </div>
    <div class="dnd-drop-targets">
      <DropTarget
        v-for="(target, index) in targets"
        :key="index"
        :accepts="target.accepts"
        :index="index"
        :currentDragItem="currentDragItem"
        @onDrop="onDrop($event)"
      />
    </div>
    <p class="drop-description">{{ dropDescription }}</p>
  </div>
</template>

<script>
import { computed, provide, reactive, readonly, ref } from "vue";
import Draggable from "./components/Draggable.vue";
import DropTarget from "./components/DropTarget.vue";

export default {
  name: "App",
  components: {
    Draggable,
    DropTarget,
  },
  setup() {
    const sources = ref([
      { type: "green" },
      { type: "green" },
      { type: "green" },
      { type: "blue" },
      { type: "blue" },
      { type: "blue" },
    ]);
    const targets = ref([
      { accepts: ["blue"] },
      { accepts: ["green"] },
      { accepts: ["blue", "green"] },
      { accepts: [] },
    ]);
    const currentDragItem = ref(null);
    const lastDrop = ref(null);

    let dragData = reactive({
      type: "",
      index: 0,
    });

    const updatedragData = (dd) => {
      dragData = { ...dd };
    };

    const classes = computed(() => {
      let c = "dnd-example";
      if (currentDragItem.value) {
        c += " dragging";
      }
      return c;
    });

    const dropDescription = computed(() => {
      if (lastDrop.value) {
        return `Dropped source ${lastDrop.value.source.type}-${lastDrop.value.source.index} on target ${lastDrop.value.target.index}`;
      }

      return "";
    });

    const onDragStart = (details) => {
      currentDragItem.value = details;
    };

    const onDragStop = () => {
      currentDragItem.value = null;
    };

    const onDrop = (target) => {
      lastDrop.value = {
        source: currentDragItem.value,
        target: target,
      };
    };

    provide("dragData", readonly(dragData));
    provide("updatedragData", updatedragData);
    provide("onDragStart", onDragStart);
    provide("onDragStop", onDragStop);

    return {
      sources,
      targets,
      classes,
      currentDragItem,
      dropDescription,
      onDrop,
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
