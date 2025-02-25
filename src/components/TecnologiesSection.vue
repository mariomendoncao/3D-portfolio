<template>
  <BaseLayout
    :progress="progress"
    :minInput="0.29"
    :fullStart="0.37"
    :fullEnd="0.5"
    :maxInput="0.57"
    class="min-h-screen flex justify-center items-center absolute inset-0 pointer-events-none custom-backdrop">
    <div class="w-full max-w-6xl text-center">
      <h1 class="main-title-text">Tecnologias Usadas</h1>
      <p class="text-white text-4xl mb-12">
        Descubra as tecnologias que impulsionam meu trabalho e transformam
        ideias em soluções inovadoras.
      </p>

      <!-- Slider -->
      <div
        class="slider"
        reverse="true"
        style="--width: 300px; --height: 300px; --quantity: 6">
        <div class="list">
          <div
            v-for="(tech, index) in technologies"
            :key="index"
            class="item bg-white/95 backdrop-blur-2xl rounded"
            :style="`--position: ${index + 1}`">
            <div
              class="flex flex-col items-center justify-center text-center p-4">
              <Icon :icon="tech.icon" class="w-24 h-24 text-white mb-3" />
              <h3 class="text-black text-lg font-bold">{{ tech.title }}</h3>
              <p class="text-gray-500 text-base">{{ tech.description }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </BaseLayout>
</template>

<script setup>
import { ref } from "vue";
import BaseLayout from "./BaseLayout.vue";
import { Icon } from "@iconify/vue";

const progress = ref(0.15);

// Tecnologias com ícones do @iconify/vue + Nome + Descrição curta
const technologies = ref([
  {
    title: "JavaScript",
    icon: "logos:javascript",
    description: "Linguagem de programação para web.",
  },
  {
    title: "Vue.js",
    icon: "logos:vue",
    description: "Framework progressivo para UIs.",
  },
  {
    title: "PHP",
    icon: "logos:php",
    description: "Linguagem de backend para web.",
  },
  {
    title: "Laravel",
    icon: "logos:laravel",
    description: "Framework PHP moderno e robusto.",
  },
  {
    title: "SQL",
    icon: "logos:mysql",
    description: "Linguagem para bancos de dados.",
  },
  {
    title: "Python",
    icon: "logos:python",
    description: "Linguagem versátil para diversos usos.",
  },
  {
    title: "Tailwind CSS",
    icon: "logos:tailwindcss-icon",
    description: "Framework CSS para estilização rápida.",
  },
  {
    title: "WordPress",
    icon: "logos:wordpress-icon",
    description: "Plataforma para criação de sites.",
  },
]);
</script>

<style>
.slider {
  width: 100%;
  height: var(--height);
  overflow: hidden;
  mask-image: linear-gradient(to right, transparent, #000 10% 90%, transparent);
}

.slider .list {
  display: flex;
  width: 100%;
  min-width: calc(var(--width) * var(--quantity));
  position: relative;
}

.slider .list .item {
  width: var(--width);
  height: var(--height);
  position: absolute;
  left: 100%;
  animation: autoRun 30s linear infinite;
  transition: filter 0.5s;
  animation-delay: calc(
    (30s / var(--quantity)) * (var(--position) - 1) - 30s
  ) !important;
  display: flex;
  align-items: center;
  justify-content: center;
}

.slider .list .item . {
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  border-radius: 12px;
  padding: 15px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.5);
}

@keyframes autoRun {
  from {
    left: 100%;
  }
  to {
    left: calc(var(--width) * -1);
  }
}

.slider:hover .item {
  animation-play-state: paused !important;
  filter: grayscale(1);
}

.slider .item:hover {
  filter: grayscale(0);
}

.slider[reverse="true"] .item {
  animation: reversePlay 30s linear infinite;
}

@keyframes reversePlay {
  from {
    left: calc(var(--width) * -1);
  }
  to {
    left: 100%;
  }
}

.custom-backdrop {
  backdrop-filter: blur(20px);
  /* Cria uma máscara radial: o centro é totalmente opaco (branco) e as bordas, transparentes */
  -webkit-mask-image: radial-gradient(circle, white 60%, transparent 100%);
  mask-image: radial-gradient(circle, white 60%, transparent 100%);
}
</style>
