<template>
  <section
    class="min-h-screen flex items-center justify-center text-white text-4xl flex-col"
    :style="{ opacity: opacity }">
    <h2 class="text-6xl font-bold text-amber-400 mb-8">Minhas Habilidades</h2>

    <div
      class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8 w-full max-w-6xl text-center">
      <p>opacity = {{ opacity }}</p>
      <div
        v-for="skill in skills"
        :key="skill.title"
        class="rounded p-4 bg-gray-500/50">
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
import { ref, watch, defineProps } from "vue";
import { Code, Brush, Layers } from "lucide-vue-next"; // Ícones para cada habilidade

// Recebe o valor de "progress" via prop
const props = defineProps(["progress"]);

// define a opacidade do elemento conforme o progresso e diminuita a opacidade conforme o scroll
const opacity = ref(1);
watch(
  () => props.progress,
  (newVal) => {
    opacity.value = 1 - newVal * 1;
  }
);

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
