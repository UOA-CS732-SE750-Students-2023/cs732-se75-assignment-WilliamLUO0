<template>
  <div>
    <h1>
      <a :href="link" @click.prevent="$emit('titleClick', link)">{{ title }}</a>
    </h1>
    <article>
      <div>
        <p v-if="showAll == false">{{ content.slice(0, 50) }}</p>
        <p v-else-if="showAll == true">{{ content }}</p>
      </div>
      <p>Hits:{{ viewCount }}</p>
      <footer v-if="content.length > 50">
        <button @click="toggleAll">{{ showAll ? 'Hide' : 'Expand' }}</button>
      </footer>
    </article>
    <slot></slot>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
defineProps(['title', 'content', 'link'])
defineEmits(['titleClick'])

const viewCount = ref(0)

const showAll = ref(false)

function toggleAll() {
  showAll.value = !showAll.value
}

onMounted(() => {
  setTimeout(() => {
    viewCount.value = 1000000
  }, 5000)
})
</script>