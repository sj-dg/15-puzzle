<script setup lang="ts">
import { ref, computed } from 'vue'

const tilesArr = ref([1, 2, 0, 4, 5, 6, 3, 8, 9, 10, 7, 12, 13, 14, 11, 15])

const zeroTilePos = computed(() => tilesArr.value.findIndex((t) => t === 0))

const shouldNotMove = (tileIndex: number) => {
  if (tileIndex === 3 && zeroTilePos.value === 4) return true
  if (tileIndex === 7 && zeroTilePos.value === 8) return true
  if (tileIndex === 11 && zeroTilePos.value === 12) return true

  if (tileIndex === 4 && zeroTilePos.value === 3) return true
  if (tileIndex === 8 && zeroTilePos.value === 7) return true
  if (tileIndex === 12 && zeroTilePos.value === 11) return true

  return false
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

const handleTitleClick = (tile: number, tileIndex: number) => {
  if (tile === 0) return

  if (
    tileIndex - 1 === zeroTilePos.value ||
    tileIndex + 1 === zeroTilePos.value ||
    tileIndex + 4 === zeroTilePos.value ||
    tileIndex - 4 === zeroTilePos.value
  )
    moveZeroTile(tileIndex)

  checkIfUserWon()
}
</script>

<template>
  <div class="grid grid-cols-4 gap-1 max-w-96 mt-16 mx-auto">
    <div
      v-for="(tile, index) in tilesArr"
      :key="index"
      class="bg-yellow-200 p-4 text-center text-xl rounded-md border border-blue-600 cursor-pointer"
      :class="{ invisible: tile === 0 }"
      role="button"
      @click="handleTitleClick(tile, index)"
    >
      {{ tile }}
    </div>
  </div>
</template>
