<script setup lang="ts">
interface BlockState {
  x: number
  y: number
  revealed: boolean
  mine?: boolean
  flagged?: boolean
  adjacentMines: number
}
const width = 10
const height = 10
const state = reactive(Array.from({ length: height }, (_, y) => Array.from({ length: width }, (_, x): BlockState => ({
  x,
  y,
  adjacentMines: 0,
  revealed: false,
}))))

function generateMines() {
  for (const row of state) {
    for (const block of row)
      block.mine = Math.random() < 0.3
  }
}
const numberColors = [
  'text-transparent',
  'text-blue-500',
  'text-green-500',
  'text-yellow-500',
  'text-orange-500',
  'text-red-500',
  'text-purple-500',
  'text-pink-500',
  'text-teal-500',
]
function onClick(block: BlockState) {
  block.revealed = true
}
function getBlockClass(block: BlockState) {
  if (!block.revealed)
    return ''
  return block.mine ? 'bg-red-500/50' : numberColors[block.adjacentMines]
}
const directions = [
  [1, 1],
  [1, 0],
  [1, -1],
  [0, -1],
  [-1, -1],
  [-1, 0],
  [-1, 1],
  [0, 1],
]

function updateNumber() {
  state.forEach((raw, y) => {
    raw.forEach((block, x) => {
      if (block.mine)
        return
      directions.forEach(([dx, dy]) => {
        const x2 = x + dx
        const y2 = y + dy
        if (x2 < 0 || x2 >= width || y2 < 0 || y2 >= height)
          return
        if (state[y2][x2].mine)
          block.adjacentMines += 1
      })
    })
  })
}
generateMines()
updateNumber()
</script>

<template>
  <div p5>
    <div
      v-for="row, y in state"
      :key="y"
      flex="~"
    >
      <button
        v-for="item, x in row"
        :key="x"
        flex="~"
        h-10 w-10 m="0.5"
        items-center
        justify-center
        border="gray-300/50 1"
        hover="bg-gray/10"
        :class="getBlockClass(item)"
        @click="onClick(item)"
      >
        <template v-if="item.revealed">
          <div v-if="item.mine" i-mdi:mine>
            x
          </div>
          <div v-else>
            {{ item.adjacentMines }}
          </div>
        </template>
      </button>
    </div>
  </div>
</template>

<route lang="yaml">
meta:
  layout: home
</route>
