<script setup lang="ts">
interface BlockState {
  x: number
  y: number
  revealed?: boolean
  mind?: boolean
  flagged?: boolean
  adjacentMines?: number
}
const width = 10
const height = 10
const state = reactive(Array.from({ length: height }, (_, y) => Array.from({ length: width }, (_, x): BlockState => ({
  x,
  y,
}))))
function onClick(x: number, y: number) {
  console.log('x: number, y: number: ', x, y)
}
function generateMines() {
  for (const row of state) {
    for (const block of row)
      block.mine = Math.random() < 0.3
  }
}
generateMines()
</script>

<template>
  <div>
    {{ data }}
    <div
      v-for="row, y in state"
      :key="y"
    >
      <button
        v-for="item, x in row"
        :key="x"
        h-8 w-8 border hover:bg-gray
        @click="onClick(x, y)"
      >
        {{ item.mine ? '#' : item.adjacentMines || '-' }}
      </button>
    </div>
  </div>
</template>

<route lang="yaml">
meta:
  layout: home
</route>
