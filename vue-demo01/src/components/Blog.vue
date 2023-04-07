<script setup>
import { computed, ref } from 'vue'
import Blogpost from './BlogPost.vue'

const blogs = ref([
  {
    id: 1,
    title: 'Vue3 Basic Tutorial',
    content: 'The content of Vue3 Basic Tutorial',
    link: '/vue-tutorial'
  },
  {
    id: 2,
    title: 'React18 Basic Tutorial',
    content: 'The content of React18 Basic Tutorial',
    link: '/react-tutorial'
  },
  {
    id: 3,
    title: 'JavaScript Basic Tutorial',
    content: 'The content of JavaScript Basic Tutorial xxxxxxxxxxxxxxxxyyyyyyyyyyyyyyyyyyy',
    link: '/javascript-tutorial'
  }
])

const total = computed(() => blogs.value.length)

const showTotal = ref(true)

function toggleTotal() {
  showTotal.value = !showTotal.value
}

const initialBlogForm = {
  title: '',
  content: '',
  link: ''
}

const blogForm = ref({ ...initialBlogForm })

function addPost() {
  blogs.value.push({
    id: blogs.value.length + 1,
    ...blogForm.value
  })
  blogForm.value = { ...initialBlogForm }
}

function handleTitleClick(link){
  console.log(link)
}

</script>

<template>
    <Blogpost @title-click="handleTitleClick" v-for="blog in blogs" :key="blog.id" v-bind="blog">
      <button>Share On Facebook</button>
    </Blogpost>
    <h3 v-if="showTotal">Total {{ total }} blogs</h3>
    <button @click="toggleTotal">{{ showTotal ? 'Hide' : 'Show' }}Total</button>
    <form @submit.prevent="addPost">
      <label for="blogTitle">Blog Title</label>
      <input type="test" id="blogTitle" v-model="blogForm.title" />
      <label for="content">Content</label>
      <textarea id="content" cols="30" rows="10" v-model="blogForm.content"></textarea>
      <label for="link">Link</label>
      <input type="text" id="link" v-model="blogForm.link" />
      <button type="submit">submit</button>
    </form>
  </template>
  
  <style scoped>
  form {
    display: grid;
    margin-top: 2em;
  }
  </style>
  