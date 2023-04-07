<template>Dynamic Route</template>
<script>
import { defineComponent, toRaw } from 'vue'
import { useRoute } from 'vue-router'
export default {
  setup() {
    const route = useRoute()
    console.log(toRaw(route))
  }
}
</script>