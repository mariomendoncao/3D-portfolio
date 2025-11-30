<script setup>
import { ref, onMounted } from 'vue';
import { Menu, X, ExternalLink } from 'lucide-vue-next';

const isOpen = ref(false);
const links = ref([]);

const toggleMenu = () => {
  isOpen.value = !isOpen.value;
};

const fetchLinks = async () => {
  try {
    const response = await fetch('/links.json');
    if (response.ok) {
      links.value = await response.json();
    } else {
      console.error('Failed to load links.json');
    }
  } catch (error) {
    console.error('Error fetching links:', error);
  }
};

onMounted(() => {
  fetchLinks();
});
</script>

<template>
  <div class="fixed top-4 right-4 z-50">
    <!-- Toggle Button -->
    <button
      @click="toggleMenu"
      class="p-2 bg-black/50 backdrop-blur-md rounded-full text-white hover:bg-black/70 transition-colors focus:outline-none focus:ring-2 focus:ring-white/50"
      aria-label="Toggle Menu"
    >
      <X v-if="isOpen" class="w-6 h-6" />
      <Menu v-else class="w-6 h-6" />
    </button>

    <!-- Dropdown Menu -->
    <transition
      enter-active-class="transition duration-200 ease-out"
      enter-from-class="transform scale-95 opacity-0"
      enter-to-class="transform scale-100 opacity-100"
      leave-active-class="transition duration-150 ease-in"
      leave-from-class="transform scale-100 opacity-100"
      leave-to-class="transform scale-95 opacity-0"
    >
      <div
        v-if="isOpen"
        class="absolute right-0 mt-2 w-56 bg-black/80 backdrop-blur-xl rounded-xl shadow-xl border border-white/10 overflow-hidden"
      >
        <div class="py-2">
          <a
            v-for="link in links"
            :key="link.url"
            :href="link.url"
            target="_blank"
            rel="noopener noreferrer"
            class="flex items-center px-4 py-3 text-sm text-gray-200 hover:bg-white/10 hover:text-white transition-colors group"
          >
            <span class="flex-1">{{ link.name }}</span>
            <ExternalLink class="w-4 h-4 opacity-50 group-hover:opacity-100 transition-opacity" />
          </a>
          <div v-if="links.length === 0" class="px-4 py-3 text-sm text-gray-400">
            Nenhum link disponível
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>
