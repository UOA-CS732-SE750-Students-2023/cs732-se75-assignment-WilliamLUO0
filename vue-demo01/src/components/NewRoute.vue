<template>
  <h1>New Route</h1>
  <button @click="goHome">Back to Home</button>
  <hr />
  <p>count: {{ store.count }}</p>
  <button @click="store.plus">Plus</button>
  <button @click="store.minus">Minus</button>
</template>

<script>
import { toRaw } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useCounterStore } from '../stores/counter.js'
export default {
  setup() {
    const route = useRoute()
    console.log(toRaw(route))
    
    const router = useRouter()
    const goHome = () => {
      router.push('/')
    }


    const store = useCounterStore()
    setTimeout(() => {
      store.plus()
    }, 1000)
    return { goHome, store }
  }
}
</script>


