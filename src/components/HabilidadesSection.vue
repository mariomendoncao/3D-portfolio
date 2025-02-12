<template>
  <section
    class="min-h-screen flex items-center justify-start text-white text-4xl flex-col"
    :class="opacity">
    <h2 class="text-6xl font-bold text-amber-400 mb-8">
      Minhas Habilidades {{ progress.toFixed(2) }}
    </h2>
    <p>Opacidade = {{ opacity }}</p>
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
  </section>
</template>

<script setup>
import { ref, defineProps, watch } from "vue";
import { Code, Brush, Layers } from "lucide-vue-next";

// Recebe o valor de "progress" via prop
const props = defineProps(["progress"]);

const opacity = ref("opacity-0");

watch(
  () => props.progress,
  (ProgressValue) => {
    opacity.value = "opacity-" + ProgressValue.toFixed(2).slice(2);
    // if (ProgressValue >= 0.05 && ProgressValue <= 0.2) {
    //   opacity.value = " animate__animated animate__fadeInLeft";
    // } else {
    //   opacity.value = " animate__animated animate__fadeOutLeft";
    // }
  }
);

// watch(
//   () => props.progress,
//   () => {
//     if (props.progress >= 0.3 && props.progress <= 0.7) {
//       opacity.value = 1;
//     } else {
//       opacity.value = 0;
//     }
//   }
// );

// Lista de cards
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
