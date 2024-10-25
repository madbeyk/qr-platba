<script setup lang="ts">
const features = [
  {
    title: 'Vue.js Powered',
    description: 'Built with the latest Vue 3 composition API for reactive and efficient components.',
    icon: '⚡'
  },
  {
    title: 'Tailwind CSS',
    description: 'Fully customizable designs with utility-first CSS framework.',
    icon: '🎨'
  },
  {
    title: 'TypeScript Ready',
    description: 'Enhanced development experience with type safety and better tooling.',
    icon: '🛡️'
  }
];
</script>

<template>
  <section id="features" class="py-20 bg-white">
    <div class="container">
      <h2 class="text-3xl font-bold text-center mb-12">Features</h2>
      <div class="grid md:grid-cols-3 gap-8">
        <div v-for="feature in features" :key="feature.title"
             class="p-6 rounded-xl border border-gray-200 hover:shadow-lg transition-shadow duration-300">
          <div class="text-4xl mb-4">{{ feature.icon }}</div>
          <h3 class="text-xl font-semibold mb-2">{{ feature.title }}</h3>
          <p class="text-gray-600">{{ feature.description }}</p>
        </div>
      </div>
    </div>
  </section>
</template>