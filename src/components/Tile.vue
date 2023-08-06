<script setup>
  import { ref,reactive,computed } from 'vue'
  const size = 64
  const props = defineProps(['tileData'])
  const tileStyle = computed(() => {
    if (props.tileData.floating) {
      return {
        'top':  '-96px',
        'left': '-96px',
        'rotate': '10deg'
      }
    }
    return {
      'top':  (props.tileData.y * size) + 'px',
      'left': (props.tileData.x * size) + 'px',
    }
  })
  const tileClass = computed(() => {
    return {
      'floating' : props.tileData.floating
    }
  })
</script>

<template>
  <div class="tile" :class="tileClass" :style="tileStyle">
    <div class="subTile tileCenter"></div>
    <div v-if="!!(Number.parseInt(tileData.openDirs[0]))" class="subTile tileOpenN"></div>
    <div v-if="!!(Number.parseInt(tileData.openDirs[1]))" class="subTile tileOpenE"></div>
    <div v-if="!!(Number.parseInt(tileData.openDirs[2]))" class="subTile tileOpenS"></div>
    <div v-if="!!(Number.parseInt(tileData.openDirs[3]))" class="subTile tileOpenW"></div>
  </div>
</template>


<style scoped>

.tile {
  width: calc(var(--tile-size));
  height: calc(var(--tile-size));
  font-size: 9px;
  text-align: center;
  border: 1px solid white;
  transition: transform 1s;
  background-color: lightgray;
  position: absolute;
  transition: top 0.3s, left 0.3s;
  box-sizing: border-box;
  background: brown;
}
.tile.floating {
  rotate: 7deg;
  box-shadow: 2px 2px 4px grey;
  border: none;
}

.subTile {
  position: absolute;
  width: calc(var(--tile-size)/3);
  height: calc(var(--tile-size)/3);
  background-color: lightgray;
  top: calc(var(--tile-size)/3);
  left: calc(var(--tile-size)/3);
}
.tileOpenN {
  top: 0;
}
.tileOpenE {
  right: 0;
  left: auto;
}
.tileOpenW {
  left: 0;
}
.tileOpenS {
  bottom: 0;
  top: auto;
}
</style>