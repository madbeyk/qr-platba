<script setup lang="ts">
//import { ref } from 'vue';
//import { Disclosure, DisclosureButton, DisclosurePanel } from '@headlessui/vue';
import { Disclosure,  DisclosurePanel } from '@headlessui/vue';
import ThemeToggle from './ThemeToggle.vue';

/*
const navigation = [
  { name: 'Home', href: '#' },
  { name: 'Features', href: '#features' },
  { name: 'About', href: '#about' },
  { name: 'Contact', href: '#contact' },
];
*/

const navigation = [
  { name: 'Home', href: '#' },
];
</script>

<template>
  <Disclosure
    as="nav"
    class="bg-white dark:bg-gray-900 shadow-md sticky top-0 z-50 transition-colors duration-300"
    
  >
    <div class="container">
      <div class="relative flex h-16 items-center justify-between">
        <div class="flex items-center">
          <img src="/logo.png" alt="K&P Logo" class="h-8 w-auto" />
        </div>

        <div class="hidden md:flex md:items-center">
          <!--
          <div class="flex space-x-8">
            <a
              v-for="item in navigation"
              :key="item.name"
              :href="item.href"
              class="text-gray-700 dark:text-gray-300 hover:text-primary dark:hover:text-primary px-3 py-2 text-sm font-medium transition-colors"
            >
              {{ item.name }}
            </a>
          </div>
          -->
          <div class="ml-8">
            <ThemeToggle />
          </div>
        </div>

        <div class="md:hidden flex items-center gap-4">
          <ThemeToggle />
          <!--
          <DisclosureButton class="p-2 text-gray-700 dark:text-gray-300">
            <svg
              class="h-6 w-6"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                v-if="!open"
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M4 6h16M4 12h16M4 18h16"
              />
              <path
                v-else
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M6 18L18 6M6 6l12 12"
              />
            </svg>
          </DisclosureButton>
          -->
        </div>
      </div>

      <DisclosurePanel class="md:hidden">
        <div class="space-y-1 px-2 pb-3 pt-2">
          <a
            v-for="item in navigation"
            :key="item.name"
            :href="item.href"
            class="block text-gray-700 dark:text-gray-300 hover:text-primary dark:hover:text-primary px-3 py-2 text-base font-medium transition-colors"
          >
            {{ item.name }}
          </a>
        </div>
      </DisclosurePanel>
    </div>
  </Disclosure>
</template>
