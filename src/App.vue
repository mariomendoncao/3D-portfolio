<!-- App.vue -->
<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import Background3D from "./components/Background3D.vue";
import HeroSection from "./components/HeroSection.vue";
import Habilidades from "./components/ServicesSection.vue";
import Tecnologies from "./components/TecnologiesSection.vue";
import MyProjects from "./components/MyProjectsSection.vue";
import Contact from "./components/ContactSection.vue";

// Valor reativo que representa o progresso da rolagem (0 a 1)
const progress = ref(0);

const updateProgress = () => {
  const scrollTop = window.pageYOffset || document.documentElement.scrollTop;
  const docHeight = document.documentElement.scrollHeight - window.innerHeight;
  progress.value = docHeight > 0 ? scrollTop / docHeight : 0;
};

onMounted(() => {
  window.addEventListener("scroll", updateProgress);
  updateProgress();
});

onUnmounted(() => {
  window.removeEventListener("scroll", updateProgress);
});
</script>

<template>
  <!-- Container principal -->
  <div class="relative">
    <p class="fixed left-2">{{ progress.toFixed(2) }}</p>
    <!-- Fundo 3D fixo -->
    <div class="fixed inset-0 z-0">
      <Background3D :progress="progress" />
    </div>

    <!-- Conteúdo da página (seções) -->
    <div class="relative z-10">
      <!-- Cada seção ocupa a tela inteira -->
      <HeroSection :progress="progress" />
      <habilidades :progress="progress" />
      <Tecnologies :progress="progress" />
      <MyProjects :progress="progress" />
      <Contact :progress="progress" />
    </div>
  </div>
</template>

<style>
/* @keyframes appear {
  from {
    opacity: 0;
    clip-path: inset(100% 100% 0 0);
  }
  to {
    opacity: 1;
    clip-path: inset(0 0 0 0);
  }
}

.secao {
  animation: appear linear;
  animation-timeline: view();
  animation-range: entry 0% cover 40%;
} */
</style>
