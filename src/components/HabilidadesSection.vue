<template>
  <BaseLayout
    :progress="progress"
    :minInput="0.03"
    :fullStart="0.1"
    :fullEnd="0.2"
    :maxInput="0.25"
    class="min-h-[var(--half-screen)] flex items-center justify-start text-white text-4xl flex-col pt-12 border-amber-500 border">
    <h1
      class="text-6xl font-bold bg-gradient-to-r from-blue-500 via-purple-500 to-pink-500 text-transparent bg-clip-text mb-12">
      Minhas Habilidades
    </h1>

    <div
      class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8 w-full max-w-6xl text-center">
      <div
        v-for="skill in skills"
        :key="skill.title"
        class="rounded h-100 w-full p-4 bg-gray-500/20 backdrop-blur-lg">
        <div class="text-6xl mb-4">
          <component :is="skill.icon" />
          <DotLottieVue autoplay loop :src="skill.animation" />
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
// import { Code, Brush, Layers } from "lucide-vue-next";
import { DotLottieVue } from "@lottiefiles/dotlottie-vue";

const progress = ref(0.15); // Exemplo: valor de progresso (pode ser atualizado dinamicamente)

const skills = ref([
  {
    title: "Desenvolvimento Web",
    description:
      "Criação de sites responsivos e interativos usando as melhores tecnologias.",
    // icon: Code,
    animation:
      "https://lottie.host/7914b575-8f01-4f70-b676-eb60183513eb/I7JXwHghdT.lottie",
  },
  {
    title: "Design UI/UX",
    description: "Experiência na criação de interfaces modernas e intuitivas.",
    // icon: Brush,
    animation:
      "https://lottie.host/c2278531-504d-4835-aeb8-c0d120aa8961/Avop8PfU5f.lottie",
  },
  {
    title: "Lógica de Programação",
    description: "Desenvolvimento de soluções inteligentes e performáticas.",
    // icon: Layers,
    animation:
      "https://lottie.host/e4b62f91-1216-46c8-9a58-84afeb2f4729/PM9rg0TDNo.lottie",
  },
]);
</script>
