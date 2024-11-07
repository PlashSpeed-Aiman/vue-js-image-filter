<script setup>
import {onMounted, ref, watch} from "vue";
import monochrome from './assets/monochrome-grunge-diagonal-lines-texture_1409-2154.avif'
const patterns = ref([
  { name: 'Zig Zag', src: './assets/monochrome-grunge-diagonal-lines-texture_1409-2154.avif' },

])

const previewCanvas = ref(null)
const previewImage = ref(null)
const currentFilter = ref(null)

// Method to apply the selected pattern as a filter
const applyFilter = (imageSource) => {
  // Create a canvas element
  const canvas = document.createElement('canvas')
  const img = new Image()
  img.src = imageSource

  img.onload = () => {
    canvas.width = img.width
    canvas.height = img.height

    // Draw the image on the canvas
    const ctx = canvas.getContext('2d')
    ctx.drawImage(img, 0, 0)

    // Create a pattern from the selected texture
    const pattern = ctx.createPattern(getPatternImage(imageSource), 'repeat')

    // Apply the pattern as a filter
    ctx.fillStyle = pattern
    ctx.fillRect(0, 0, canvas.width, canvas.height)

    // Update the current filter
    currentFilter.value = canvas.toDataURL()

    return currentFilter.value
  }
}

// Helper method to get the pattern image element
const getPatternImage = (src) => {
  const patternImage = new Image()
  patternImage.src = src
  return patternImage
}

const handleImageUpload = (event) => {
  const file = event.target.files[0]
  if (file) {
    previewImage.value = URL.createObjectURL(file)
    currentFilter.value = null
    drawImageOnCanvas()
  }
}

const drawImageOnCanvas = () => {
  if (previewImage.value && previewCanvas.value) {
    const canvas = previewCanvas.value
    const ctx = canvas.getContext('2d')
    const img = new Image()
    img.src = previewImage.value

    img.onload = () => {
      canvas.width = img.width
      canvas.height = img.height
      ctx.drawImage(img, 0, 0)
    }
  }
}

onMounted(() => {
  drawImageOnCanvas()
})

watch(currentFilter, () => {
  if (previewCanvas.value) {
    const canvas = previewCanvas.value
    const ctx = canvas.getContext('2d')
    const img = new Image()
    img.src = currentFilter.value

    img.onload = () => {
      ctx.drawImage(img, 0, 0)
    }
  }
})
</script>

<template>
  <nav>
    <ul class="flex gap-2 m-2">
      <li>
        <label for="image-upload" class="flex text-sm bg-blue-400 rounded px-2 py-2 cursor-pointer">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
            <path stroke-linecap="round" stroke-linejoin="round" d="M3 16.5v2.25A2.25 2.25 0 0 0 5.25 21h13.5A2.25 2.25 0 0 0 21 18.75V16.5m-13.5-9L12 3m0 0 4.5 4.5M12 3v13.5" />
          </svg>
          Upload
        </label>
        <input type="file" id="image-upload" class="hidden" @change="handleImageUpload" accept="image/*" />
      </li>
      <li><button class="flex text-sm bg-blue-400 rounded px-2 py-2">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
          <path stroke-linecap="round" stroke-linejoin="round" d="M3 16.5v2.25A2.25 2.25 0 0 0 5.25 21h13.5A2.25 2.25 0 0 0 21 18.75V16.5M16.5 12 12 16.5m0 0L7.5 12m4.5 4.5V3" />
        </svg>
        Save</button></li>
      <li><button class="flex text-sm bg-blue-400 rounded px-2 py-2"><svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
        <path stroke-linecap="round" stroke-linejoin="round" d="m15 15 6-6m0 0-6-6m6 6H9a6 6 0 0 0 0 12h3" />
      </svg>
        Redo</button></li>
      <li><button class="flex text-sm bg-blue-400 rounded px-2 py-2">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
          <path stroke-linecap="round" stroke-linejoin="round" d="M9 15 3 9m0 0 6-6M3 9h12a6 6 0 0 1 0 12h-3" />
        </svg>
        Undo</button></li>
      <li><button class="flex text-sm bg-blue-400 rounded px-2 py-2">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="size-6">
          <path stroke-linecap="round" stroke-linejoin="round" d="m14.74 9-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 0 1-2.244 2.077H8.084a2.25 2.25 0 0 1-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 0 0-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 0 1 3.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 0 0-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 0 0-7.5 0" />
        </svg>
        Delete</button></li>
    </ul>
  </nav>
  <main class="flex">
    <section class="pattern-bar  max-w-xs  bg-amber-100 h-screen p-3 ">
      <div class="grid grid-cols-2 gap-3">
        <div
            v-for="pattern in patterns"
            :key="pattern.name"
            class="bg-white rounded-md shadow h-32 w-32 cursor-pointer"
            @click="applyFilter(pattern.src)"
        >
          <img class="h-24" :src="applyFilter(pattern.src)" />
          <span class="m-1">{{ pattern.name }}</span>
        </div>
      </div>
    </section>
    <section class="image-preview flex items-center justify-center h-full w-full">
      <img v-if="previewImage" :src="previewImage" class="max-h-full max-w-full" />
      <span v-else class="text-gray-400">No image uploaded</span>
    </section>
  </main>
</template>

<style scoped>

</style>
