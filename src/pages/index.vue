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
let mineGenerated = false
const dev = true
function onClick(block: BlockState) {
  if (!mineGenerated) {
    generateMines(block)
    mineGenerated = true
  }
  block.revealed = true

  expendZero(block)
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
  state.forEach((raw) => {
    raw.forEach((block) => {
      if (block.mine)
        return
      getSiblings(block).forEach((b) => {
        if (b.mine)
          block.adjacentMines += 1
      })
    })
  })
}
function getSiblings(block: BlockState) {
  return directions.map(([dx, dy]) => {
    const x2 = block.x + dx
    const y2 = block.y + dy
    if (x2 < 0 || x2 >= width || y2 < 0 || y2 >= height)
      return undefined
    return state[y2][x2]
  }).filter(Boolean) as BlockState[]
}

function generateMines(initial: BlockState) {
  for (const row of state) {
    for (const block of row) {
      if (Math.abs(initial.x - block.x) <= 1)
        continue
      if (Math.abs(initial.y - block.y) <= 1)
        continue
      block.mine = Math.random() < 0.3
    }
  }
  updateNumber()
}

function expendZero(block: BlockState) {
  if (block.adjacentMines)
    return
  getSiblings(block).forEach((s) => {
    if (!s.revealed) {
      s.revealed = true
      expendZero(s)
    }
  })
}
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
        border="gray-300/50 2"
        hover="bg-gray/10"
        :class="getBlockClass(item)"
        @click="onClick(item)"
      >
        <template v-if="item.revealed || dev">
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
