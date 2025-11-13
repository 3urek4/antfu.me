<script setup lang="ts">
import { ref, computed } from 'vue'
import PhotoGrid from './photos/PhotoGrid.vue'
import { photos } from '~/../photos/data'

// 根据 year 分组
const photosByYear = computed(() => {
  const groups: Record<string, typeof photos> = {}
  for (const photo of photos) {
    const year = new Date(photo.date).getFullYear().toString()
    if (!groups[year]) {
      groups[year] = []
    }
    groups[year].push(photo)
  }
  // 按年份倒序
  return Object.fromEntries(Object.entries(groups).sort(([a], [b]) => parseInt(b) - parseInt(a)))
})

// 展开/折叠状态
const expandedYears = ref<string[]>(Object.keys(photosByYear.value))

// 切换年份展开/折叠
const toggleYear = (year: string) => {
  const index = expandedYears.value.indexOf(year)
  if (index >= 0) {
    expandedYears.value.splice(index, 1)
  } else {
    expandedYears.value.push(year)
  }
}

// 检查年份是否展开
const isYearExpanded = (year: string) => {
  return expandedYears.value.includes(year)
}
</script>

<template>
  <div class="photos-view">
    <!-- 统计信息 -->
    <div class="photos-stats mb-8 text-center text-gray-600 dark:text-gray-400">
      共 {{ photos.length }} 张照片
    </div>

    <!-- 照片按年份分组展示 -->
    <div class="photos-by-year space-y-8">
      <div v-for="[year, yearPhotos] in Object.entries(photosByYear)" :key="year">
        <!-- 年份标题和展开/折叠按钮 -->
        <div 
          class="year-header flex items-center justify-between cursor-pointer mb-4"
          @click="toggleYear(year)"
        >
          <h2 class="text-3xl font-bold text-gray-800 dark:text-white">{{ year }}</h2>
          <button 
            class="text-2xl font-bold text-gray-500 dark:text-gray-400 hover:text-gray-700 dark:hover:text-gray-200"
          >
            {{ isYearExpanded(year) ? '▼' : '▶' }}
          </button>
        </div>

        <!-- 年份下的照片网格 -->
        <div v-if="isYearExpanded(year)">
          <PhotoGrid :photos="yearPhotos" />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.photos-view {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
}

@media (max-width: 640px) {
  .photos-view {
    padding: 1rem;
  }
}
</style>