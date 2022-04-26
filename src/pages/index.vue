<script setup lang="ts">
import type { BlockState } from '~/types/mine'
import Timer from '~/components/Timer.vue'
const pageTitle = useTitle()
pageTitle.value = 'mineSweeper'

const HEIGHT = 10
const WIDTH = 10
const mineState = reactive(Array.from({ length: HEIGHT },
  (_, x) => Array.from({ length: WIDTH },
    (_, y): BlockState => ({ x, y, revealed: false, adjacentMines: 0, flagged: false }))),
)
defineComponent({ Timer })
const timer = ref<any>()

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

function finishGame() {
  for (let y = 0; y < HEIGHT; y++) {
    for (let x = 0; x < WIDTH; x++) {
      const mine = mineState[y][x]
      if (mine.mine)
        mine.revealed = true
    }
  }
  timer.value.stop()
  setTimeout(() => { alert('Game Over') }, 1000)
}

const maxRevealDepth = 4
function reveal(mine: BlockState, depth = 0) {
  if (depth >= maxRevealDepth)
    return
  if (mine.revealed || mine.flagged)
    return

  mine.revealed = true
  if (mine.mine) {
    finishGame()
    return
  }

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

function initBlocks(mineNum = 5) {
  for (let y = 0; y < HEIGHT; y++) {
    for (let x = 0; x < WIDTH; x++) {
      const state = mineState[y][x]
      state.adjacentMines = 0
      state.mine = false
      state.revealed = false
      state.flagged = false
    }
  }
  generateMines(mineNum)
  calcAdjacentMines()
  timer.value.reset()
}

function flag(mine: BlockState) {
  if (mine.revealed)
    return
  mine.flagged = !mine.flagged
}
const blockTextColors = [
  'text-gray-500',
  'text-blue-500',
  'text-pink-500',
  'text-green-500',
  'text-red-500',
  'text-emerald-500',
  'text-amber-500',
]

const dev = ref(false)

function getBlockClass(state: BlockState) {
  if (state.flagged)
    return 'dark:bg-white bg-red-100'

  if (!state.revealed && !dev.value)
    return 'bg-gray-500/50'
    // return blockTextColors[state.adjacentMines]

  // return state.mine ? 'text-red' : 'dark:text-blueGray text-black'
  return state.mine ? 'bg-red-400/90' : blockTextColors[state.adjacentMines]
}

onMounted(() => initBlocks(10))
</script>

<template>
  <div>
    <a font-bold text-blueGray text-2xl> MineSweeper </a>
    <br>
    <div i-carbon-campsite text-4xl inline-block />
    <Timer ref="timer" />
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
          @contextmenu.prevent="flag(state)"
        >
          <template v-if="state.revealed || dev">
            <div v-if="state.mine" class="text-white">
              <div i-mdi-mine />
            </div>
            <div v-else>
              {{ state.adjacentMines }}
            </div>
          </template>
          <template v-else>
            <div v-if="state.flagged" dark:text-emerald-900 text-red-500>
              <div i-carbon:flag-filled />
            </div>
          </template>
        </button>
      </div>
    </div>
    <button
      p1 border dark:bg-gray-100 bg-purple-200
      dark:text-amber-500 text-green-500 font-bold text-2xl rounded-2xl
      @click="initBlocks(10)"
    >
      restart
    </button>
    <!-- <Qin /> -->
  </div>
</template>
