<script setup lang="ts">
import { ref, computed } from 'vue'

const MATRIX_SIZE = 4
const MATRIX_ARR = [1, 2, 0, 4, 5, 6, 3, 8, 9, 10, 7, 12, 13, 14, 11, 15]

const hasUserWon = ref(false)
const tilesArr = ref([...MATRIX_ARR])

const zeroTilePos = computed(() => tilesArr.value.findIndex((t) => t === 0))

const matrixCoordinates = computed(() => {
  return tilesArr.value.map((_, i) => {
    return [Math.floor(i / MATRIX_SIZE), i % MATRIX_SIZE]
  })
})

const shouldNotMove = (tileIndex: number) => {
  const tileCoordinates = matrixCoordinates.value[tileIndex]
  const zeroTileCoordinates = matrixCoordinates.value[zeroTilePos.value]

  const xDiff = Math.abs(tileCoordinates[0] - zeroTileCoordinates[0])
  const yDiff = Math.abs(tileCoordinates[1] - zeroTileCoordinates[1])

  const hasBothCoordsChanged = xDiff > 0 && yDiff > 0

  if (hasBothCoordsChanged) return true

  return xDiff > 1 || yDiff > 1
}

const handleTitleClick = (tile: number, tileIndex: number) => {
  if (tile === 0) return
  if (shouldNotMove(tileIndex)) return
  replaceTiles(tileIndex)
  checkIfUserWon()
}

const replaceTiles = async (tileIndex: number) => {
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
    hasUserWon.value = true
  }
}

const reset = () => {
  hasUserWon.value = false
  tilesArr.value = MATRIX_ARR
}
</script>

<template>
  <section class="mt-16 max-w-96 mx-auto">
    <div class="grid grid-cols-4 gap-1">
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
    <div class="grid grid-cols-2 gap-3 w-full mt-2 rever">
      <button class="bg-gray-300 rounded-md py-2 px-4" @click="reset">Reset</button>
      <p
        class="border border-gray-300 rounded-md grid place-items-center animate-pulse"
        v-if="hasUserWon"
      >
        You have won 🎉
      </p>
    </div>
  </section>
</template>
