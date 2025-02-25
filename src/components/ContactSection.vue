<template>
  <BaseLayout
    :progress="progress"
    :minInput="0.75"
    :fullStart="0.8"
    :fullEnd="1.1"
    :maxInput="1.1"
    class="min-h-screen flex items-center justify-start text-white text-4xl flex-col pt-12">
    <h1 class="main-title-text">Meus Contatos</h1>
    <!-- <p>min-h-[var(--half-screen)]</p> -->
    <div
      class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8 w-full max-w-6xl text-center">
      <div
        v-for="skill in skills"
        :key="skill.title"
        class="rounded h-100 w-full p-4 bg-gray-300/20 backdrop-blur-lg">
        <div class="text-6xl mb-4">
          <component :is="skill.icon" />
          <DotLottieVue autoplay loop :src="skill.animation" />
        </div>
        <h3 class="title-text">
          {{ skill.title }}
        </h3>
        <p class="text-white text-xl">{{ skill.description }}</p>
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
      "Sites modernos, rápidos e responsivos com tecnologias de ponta.",
    animation:
      "https://lottie.host/7914b575-8f01-4f70-b676-eb60183513eb/I7JXwHghdT.lottie",
  },
  {
    title: "Design UI/UX",
    description:
      "Interfaces intuitivas e elegantes, focadas na experiência do usuário.",
    animation:
      "https://lottie.host/c2278531-504d-4835-aeb8-c0d120aa8961/Avop8PfU5f.lottie",
  },
  {
    title: "Soluções Digitais",
    description: "Sistemas eficientes e personalizados para o seu negócio.",
    animation:
      "https://lottie.host/e4b62f91-1216-46c8-9a58-84afeb2f4729/PM9rg0TDNo.lottie",
  },
]);
</script>
