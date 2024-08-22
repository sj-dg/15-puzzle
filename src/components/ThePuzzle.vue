<script setup lang="ts">
import { ref, computed } from 'vue'

const MATRIX_SIZE = 4

const tilesArr = ref([1, 2, 0, 4, 5, 6, 3, 8, 9, 10, 7, 12, 13, 14, 11, 15])

const zeroTilePos = computed(() => tilesArr.value.findIndex((t) => t === 0))

const coordinates = computed(() => {
  return tilesArr.value.map((_, i) => {
    return [Math.floor(i / MATRIX_SIZE), i % MATRIX_SIZE]
  })
})

const shouldNotMove = (tileIndex: number) => {
  const tileCoordinates = coordinates.value[tileIndex]
  const zeroTileCoordinates = coordinates.value[zeroTilePos.value]

  const xDiff = Math.abs(tileCoordinates[0] - zeroTileCoordinates[0])
  const yDiff = Math.abs(tileCoordinates[1] - zeroTileCoordinates[1])

  return xDiff > 1 || yDiff > 1
}

const handleTitleClick = (tile: number, tileIndex: number) => {
  if (tile === 0) return

  const isMovingBack = tileIndex - 1 === zeroTilePos.value
  const isMovingForward = tileIndex + 1 === zeroTilePos.value
  const isMovingUp = tileIndex - MATRIX_SIZE === zeroTilePos.value
  const isMovingDown = tileIndex + MATRIX_SIZE === zeroTilePos.value

  if (isMovingBack || isMovingForward || isMovingUp || isMovingDown) moveZeroTile(tileIndex)

  checkIfUserWon()
}

const moveZeroTile = async (tileIndex: number) => {
  if (shouldNotMove(tileIndex)) return
  const temp = tilesArr.value[tileIndex]
  const zeroIndex = zeroTilePos.value
  tilesArr.value.splice(tileIndex, 1)
  tilesArr.value.splice(tileIndex, 0, 0)
  tilesArr.value.splice(zeroIndex, 1, temp)
}

const checkIfUserWon = () => {
  let hasWon = true
  for (let i = 0; i < tilesArr.value.length - 1; i++) {
    hasWon = hasWon && tilesArr.value[i] === i + 1
  }
  if (hasWon) {
    alert('Bingo!')
  }
}
</script>

<template>
  <div class="grid grid-cols-4 gap-1 mt-16 max-w-96 mx-auto">
    <div
      v-for="(tile, index) in tilesArr"
      :key="index"
      class="bg-yellow-200 py-6 px-4 text-center text-xl rounded-md border border-blue-600 cursor-pointer"
      :class="{ invisible: tile === 0 }"
      role="button"
      @click="handleTitleClick(tile, index)"
    >
      {{ tile }}
    </div>
  </div>
</template>
