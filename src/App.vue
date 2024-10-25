<script setup lang="ts">
import Header from './components/Header.vue';
import Hero from './components/Hero.vue';
import Features from './components/Features.vue';
import QRGenerator from './components/QRGenerator.vue';
import Footer from './components/Footer.vue';
</script>

<template>
  <div
    class="min-h-screen bg-white dark:bg-gray-900 transition-colors duration-300"
  >
    <Header />
    <main>
      <!---<Hero />-->
      <!---<Features />-->
      <QRGenerator />
    </main>
    <Footer />
  </div>
</template>
