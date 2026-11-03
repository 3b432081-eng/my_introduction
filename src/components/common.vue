<script setup>
defineProps({
  title: {
    type: String,
    required: true
  },
  content: {
    type: String,
    required: true
  }
})
</script>

<template>
  <article class="card">
    <h3>{{ title }}</h3>
    <p>{{ content }}</p>
  </article>
</template>