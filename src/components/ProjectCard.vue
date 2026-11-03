<script setup>
defineProps({
  title: {
    type: String,
    required: true
  },
  content: {
    type: String,
    required: true
  },
  tag: {
    type: String,
    default: ''
  }
})
</script>

<template>
  <article class="card project-card">
    <div>
      <div v-if="tag" class="project-tag">{{ tag }}</div>
      <h3>{{ title }}</h3>
      <p v-if="content">{{ content }}</p>
    </div>
  </article>
</template>
