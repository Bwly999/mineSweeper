<script setup lang="ts">

interface BlockState {
  x: number
  y: number
  revealed: boolean
  mine?: boolean
  flagged?: boolean
  adjacentMines: number
}
const HEIGHT = 10
const WIDTH = 10
const mineState = reactive(Array.from({ length: HEIGHT },
  (_, x) => Array.from({ length: WIDTH },
    (_, y): BlockState => ({ x, y, revealed: false, adjacentMines: 0 }))),
)

function generateMines(count: number) {
  const mines = []
  while (mines.length < count) {
    const x = Math.floor(Math.random() * WIDTH)
    const y = Math.floor(Math.random() * HEIGHT)
    const mine = mineState[y][x]
    if (!mine.mine) {
      mine.mine = true
      mines.push(mine)
    }
  }
  return mines
}

function calcAdjacentMines() {
  for (let y = 0; y < HEIGHT; y++) {
    for (let x = 0; x < WIDTH; x++) {
      const mine = mineState[y][x]
      if (mine.mine) {
        for (let dy = -1; dy <= 1; dy++) {
          for (let dx = -1; dx <= 1; dx++) {
            if (dx === 0 && dy === 0)
              continue
            const adjX = x + dx
            const adjY = y + dy
            if (adjX >= 0 && adjX < WIDTH && adjY >= 0 && adjY < HEIGHT) {
              const adjMine = mineState[adjY][adjX]
              if (!adjMine.mine)
                adjMine.adjacentMines = (adjMine.adjacentMines || 0) + 1
            }
          }
        }
      }
    }
  }
}

const maxRevealDepth = 4
function reveal(mine: BlockState, depth = 0) {
  if (depth >= maxRevealDepth)
    return
  if (mine.revealed || mine.flagged)
    return
  mine.revealed = true
  if (mine.mine)
    return
  if (mine.adjacentMines === 0) {
    for (let dy = -1; dy <= 1; dy++) {
      for (let dx = -1; dx <= 1; dx++) {
        if (dx === 0 && dy === 0)
          continue
        const adjX = mine.x + dx
        const adjY = mine.y + dy
        if (Math.random() < 0.3 && adjX >= 0 && adjX < WIDTH && adjY >= 0 && adjY < HEIGHT)
          reveal(mineState[adjX][adjY], depth + 1)
      }
    }
  }
}

function getBlockClass(state: BlockState) {
  if (!state.revealed)
    return 'bg-gray-500/50'

  return state.mine ? 'text-red' : 'dark:text-blueGray text-black'
}

generateMines(10)
calcAdjacentMines()
</script>

<template>
  <div>
    <a font-bold text-blueGray text-2xl> MineSweeper </a>
    <br>
    <div i-carbon-campsite text-4xl inline-block />
    <div p-5>
      <div v-for="line, x in mineState" :key="x" flex="~" items-center justify-center>
        <button
          v-for="state, y in line"
          :key="y"
          m-1 w-10 h-10 border
          flex="~" items-center justify-center
          hover:bg-blueGray
          :class="getBlockClass(state)"
          @click="reveal(state)"
        >
          <template v-if="state.revealed">
            <div v-if="state.mine" class="text-red">
              <div i-mdi-mine />
            </div>
            <div v-else>
              {{ state.adjacentMines }}
            </div>
          </template>
        </button>
      </div>
    </div>
  </div>
</template>
