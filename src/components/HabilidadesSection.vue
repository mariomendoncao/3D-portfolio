<template>
  <BaseLayout
    :progress="progress"
    :minInput="0.03"
    :fullStart="0.1"
    :fullEnd="0.2"
    :maxInput="0.25">
    <h2 class="text-6xl font-bold text-amber-400 mb-8">
      Minhas Habilidades {{ progress.toFixed(2) }}
    </h2>
    <div
      class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8 w-full max-w-6xl text-center">
      <div
        v-for="skill in skills"
        :key="skill.title"
        class="rounded p-4 bg-gray-500/50 transition-transform transform hover:scale-105">
        <div class="text-6xl mb-4">
          <component :is="skill.icon" />
        </div>
        <h3 class="text-2xl font-semibold mb-2">{{ skill.title }}</h3>
        <p class="text-gray-300 text-xl">{{ skill.description }}</p>
      </div>
    </div>
  </BaseLayout>
</template>

<script setup>
import { ref } from "vue";
import BaseLayout from "./BaseLayout.vue";
import { Code, Brush, Layers } from "lucide-vue-next";

const progress = ref(0.15); // Exemplo: valor de progresso (pode ser atualizado dinamicamente)

const skills = ref([
  {
    title: "Desenvolvimento Web",
    description:
      "Criação de sites responsivos e interativos usando as melhores tecnologias.",
    icon: Code,
  },
  {
    title: "Design UI/UX",
    description: "Experiência na criação de interfaces modernas e intuitivas.",
    icon: Brush,
  },
  {
    title: "Lógica de Programação",
    description: "Desenvolvimento de soluções inteligentes e performáticas.",
    icon: Layers,
  },
]);
</script>
