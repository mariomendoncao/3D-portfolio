<!-- App.vue -->
<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import Background3D from "./components/Background3D.vue";
import HeroSection from "./components/HeroSection.vue";

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
    <!-- Fundo 3D fixo -->
    <div class="fixed inset-0 z-0">
      <Background3D :progress="progress" />
    </div>

    <!-- Conteúdo da página (seções) -->
    <div class="relative z-10">
      <!-- Cada seção ocupa a tela inteira -->
      <HeroSection />
      <section
        class="min-h-screen flex items-center justify-center bg-gray-800/5 text-white text-4xl">
        <div>Habilidades</div>
      </section>
      <section
        class="min-h-screen flex items-center justify-center bg-gray-700/10 text-white text-4xl">
        <div>Tecnologias</div>
      </section>
      <section
        class="min-h-screen flex items-center justify-center bg-gray-600/15 text-white text-4xl">
        <div>Projetos</div>
      </section>
      <section
        class="min-h-screen flex items-center justify-center bg-gray-500/25 text-white text-4xl">
        <div>Contato</div>
      </section>
    </div>
  </div>
</template>
