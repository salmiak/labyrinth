<script>
import { ref,reactive, computed } from 'vue'
import BoardTile from './Tile.vue'

const lockedTiles = [
  {x: 0, y: 0, openDirs: "0110"},
  {x: 2, y: 0, openDirs: "0111"},
  {x: 4, y: 0, openDirs: "0111"},
  {x: 6, y: 0, openDirs: "0011"},

  {x: 0, y: 2, openDirs: "1110"},
  {x: 2, y: 2, openDirs: "1110"},
  {x: 4, y: 2, openDirs: "0111"},
  {x: 6, y: 2, openDirs: "1011"},

  {x: 0, y: 4, openDirs: "1110"}, // {n: true, e: true, s: true}},
  {x: 2, y: 4, openDirs: "1101"}, // {n: true, e: true, w: true}},
  {x: 4, y: 4, openDirs: "1011"}, // {n: true, w: true, s: true}},
  {x: 6, y: 4, openDirs: "1011"}, // {n: true, w: true, s: true}},

  {x: 0, y: 6, openDirs: "1100"}, // {n: true, e: true}},
  {x: 2, y: 6, openDirs: "1101"}, // {n: true, e: true, w: true}},
  {x: 4, y: 6, openDirs: "1101"}, // {n: true, w: true, e: true}},
  {x: 6, y: 6, openDirs: "1001"}, // {n: true, w: true}},
]

String.prototype.shuffle = function () {
    var a = this.split(""),
        n = a.length;

    for(var i = n - 1; i > 0; i--) {
        var j = Math.floor(Math.random() * (i + 1));
        var tmp = a[i];
        a[i] = a[j];
        a[j] = tmp;
    }
    return a.join("");
}
const freeTiles = (("L".repeat(13)) + ("I".repeat(13)) + ("T".repeat(8))).shuffle().split('')

export default {
  // `setup` is a special hook dedicated for the Composition API.
  components: {
    BoardTile
  },
  setup() {

    const boardBootstrap = []
    const size = 7

    function getRandomBool() {
      return Math.random() < 0.5;
    }

    function getDirString(dir) {
      switch(dir) {
        case 'L':
          return "1100"
          break;
        case 'I':
          return "0101"
          break;
        case 'T':
          return "1110"
          break;
      }
    }

    function rotateTile(tileOpenDirs, rnd) {
      tileOpenDirs = tileOpenDirs + tileOpenDirs + tileOpenDirs
      if (rnd) {
        tileOpenDirs = "0".repeat(Math.floor(Math.random() * 4)) + tileOpenDirs
      } else {
        tileOpenDirs = "0" + tileOpenDirs
      }
      return tileOpenDirs.slice(4,8)
    }

    for (var i = 0; i< size*size; i++) {

      const x = i % size
      const y = Math.floor(i/size)

      const lockedTile = lockedTiles.find(tile => tile.y == y && tile.x == x)

      if (lockedTile) {
        boardBootstrap.push(lockedTile)
      } else {
        const dir = freeTiles.pop()
        boardBootstrap.push({
          id: i,
          x: i % size,
          y: Math.floor(i/size),
          openDirs: rotateTile(getDirString(dir), true)
        })
      }
    }

    boardBootstrap.push({
      id: size * size,
      floating: true,
      openDirs: getDirString(freeTiles[0])
    })

    const board = reactive(boardBootstrap)
    const boardSize = ref(size)

    function insertTile (dir, idx) {  
      const floatingTile = board.find(tile => tile.floating)  
      floatingTile.x = 0
      floatingTile.y = 0
      switch (dir) {
        case 'S':
          board.filter(tile => tile.x == idx).forEach(tile => {
            if (tile.y < boardSize.value - 1) {
              tile.y = (boardSize.value + tile.y + 1) % boardSize.value
            } else {
              tile.y = undefined,
              tile.x = undefined,
              tile.floating = true
            }
          }) 
          floatingTile.y = 0
          floatingTile.x = idx
          break;
        case 'N':
          board.filter(tile => tile.x == idx).forEach(tile => {
            if (tile.y > 0) {
              tile.y = (boardSize.value + tile.y - 1) % boardSize.value
            } else {
              tile.y = undefined,
              tile.x = undefined,
              tile.floating = true
            }
          })
          floatingTile.x = idx
          floatingTile.y = boardSize.value - 1
          break;
        case 'W':
          board.filter(tile => tile.y == idx).forEach(tile => {
            if (tile.x > 0) {
              tile.x = (boardSize.value + tile.x - 1) % boardSize.value
            } else {
              tile.y = undefined,
              tile.x = undefined,
              tile.floating = true
            }
          })
          floatingTile.x = boardSize.value - 1
          floatingTile.y = idx
          break;
        case 'E':
          board.filter(tile => tile.y == idx).forEach(tile => {
            if (tile.x < boardSize.value - 1) {
              tile.x = (boardSize.value + tile.x + 1) % boardSize.value
            } else {
              tile.y = undefined,
              tile.x = undefined,
              tile.floating = true
            }
          }) 
          floatingTile.x = 0
          floatingTile.y = idx
          break;
        default:
          console.error('Wrong direction')
          break;
      }
      floatingTile.floating = false
    }

    function rotateFloatingTile() {
      const tile = board.find(tile => tile.floating)
      tile.openDirs = rotateTile(tile.openDirs)
    }

    // expose the ref to the template
    return {
      board,
      boardSize,
      insertTile,
      rotateFloatingTile
    }
  }
}

</script>

<template>

  <div id="board" class="board">

    <button 
      v-for="col in 3" 
      @click="insertTile('N', col*2-1)"
      class="dirButton dirButtonN"
      :style="{'left':(col*2-1)*64 + 'px'}"
      >&uarr;</button><br/>

    <button 
      v-for="col in 3" 
      @click="insertTile('W', col*2-1)"
      class="dirButton dirButtonW"
      :style="{'top':(col*2-1)*64 + 'px'}"
      >&larr;</button><br/>

    <button 
      v-for="col in 3" 
      @click="insertTile('E', col*2-1)"
      class="dirButton dirButtonE"
      :style="{'top':(col*2-1)*64 + 'px'}"
      >&rarr;</button><br/>

    <button 
      v-for="col in 3" 
      @click="insertTile('S', col*2-1)"
      class="dirButton dirButtonS"
      :style="{'left':(col*2-1)*64 + 'px'}"
      >&darr;</button>

    
    <button 
      class="rotateButton"
      @click="rotateFloatingTile()"
      >Rotera</button>

    <BoardTile v-for="tile in board" :tile-data="tile" />
  </div>

</template>


<style scoped>
.board {
  width: calc(var(--board-size) * var(--tile-size));
  height: calc(var(--board-size) * var(--tile-size));
  border: 10px solid lightgoldenrodyellow;
  background: lightgoldenrodyellow;
  border-radius: 14px;
  position: relative;
  box-sizing: content-box;
}

.rotateButton {
  position: relative;
  left: -96px;
  top: -64px;
}

.dirButton {
  position: absolute;
  width: 64px;
  height: 24px;
}
.dirButtonN, .dirButtonS {
  width: 64px;
  height: 24px;
}
.dirButtonN {
  bottom: -32px;
}
.dirButtonS {
  top: -32px;
}
.dirButtonW, .dirButtonE {
  height: 64px;
  width: 24px;
}
.dirButtonW {
  right: -32px;
}
.dirButtonE {
  left: -32px;
}
</style>