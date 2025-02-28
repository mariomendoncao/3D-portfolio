<template>
  <BaseLayout
    :progress="progress"
    :minInput="0.55"
    :fullStart="0.6"
    :fullEnd="0.7"
    :maxInput="0.75"
    class="min-h-screen flex flex-col items-center justify-center container mx-auto px-4 py-8">
    <h1 class="main-title-text text-5xl font-bold mb-8 text-white text-center">
      Meus Projetos Recentes
    </h1>

    <!-- Slider principal para os projetos -->
    <Splide :options="splideOptions" class="w-full max-w-5xl">
      <SplideSlide
        v-for="project in projects"
        :key="project.title"
        class="px-2">
        <div
          class="bg-gray-300/50 backdrop-blur-lg rounded-lg p-12 shadow-lg flex flex-col md:flex-row items-center">
          <!-- Texto do Projeto (centralizado e com fontes maiores) -->
          <div class="flex-1 p-4 text-center">
            <h2 class="title-text text-3xl font-semibold mb-4 text-gray-900">
              {{ project.title }}
            </h2>
            <p class="text-xl text-start text-gray-800">
              {{ project.description }}
            </p>
          </div>
          <!-- Fotos do Projeto: posicionadas de forma "espalhada" -->
          <div class="flex-1 p-4 relative">
            <div class="relative w-full h-80">
              <img
                v-for="(img, index) in project.images"
                :key="img.id"
                :src="img.url"
                :alt="img.alt"
                :class="[
                  getImageClasses(index),
                  'absolute',
                  'rounded-xl',
                  'shadow-md',
                  'object-cover',
                  'transition-transform',
                  'duration-300',
                  'hover:scale-105',
                  'animate-image',
                ]"
                :style="{ animationDelay: index * 2 + 's' }" />
            </div>
          </div>
        </div>
      </SplideSlide>
    </Splide>
  </BaseLayout>
</template>

<script setup>
import { ref } from "vue";
import BaseLayout from "./BaseLayout.vue";
// Importa os componentes do Vue Splide
import { Splide, SplideSlide } from "@splidejs/vue-splide";
import "@splidejs/splide/dist/css/splide.min.css";

const progress = ref(0.15);

const projects = ref([
  {
    title: "Pupsi",
    description:
      "Sistema desenvolvido para gerenciamento de escalas de trabalho, voltado principalmente para órgão de controle de tráfego aéreo. Desenvolvido utilizando a stack Laravel, Vue e Tailwind.",
    images: [
      { id: 1, url: "/images/pupsi.png", alt: "Screenshot do projeto Pupsi" },
      {
        id: 2,
        url: "/images/pupsi_2.png",
        alt: "Outra captura do projeto Pupsi",
      },
      {
        id: 3,
        url: "/images/pupsi_3.png",
        alt: "Captura adicional do projeto Pupsi",
      },
    ],
  },
  {
    title: "Pupsi LRO",
    description:
      "Sistema desenvolvido para gerenciamento de informações de turnos de trabalho em órgão de controle de tráfego aéreo. Desenvolvido utilizando a stack Laravel, Vue e Tailwind.",
    images: [
      {
        id: 4,
        url: "/images/pupsi_lro.png",
        alt: "Screenshot do projeto Pupsi LRO",
      },
      {
        id: 5,
        url: "/images/pupsi_lro_2.png",
        alt: "Outra captura do projeto Pupsi LRO",
      },
      {
        id: 6,
        url: "/images/pupsi_lro_3.png",
        alt: "Captura adicional do projeto Pupsi LRO",
      },
    ],
  },
]);

// Configurações do Splide para o slider
const splideOptions = {
  type: "loop",
  perPage: 1,
  autoplay: true,
  pauseOnHover: true,
  pagination: true,
  arrows: true,
};

// Função que retorna classes diferentes para cada imagem, definindo tamanhos e posições
const getImageClasses = (index) => {
  switch (index) {
    case 0:
      return "w-[300px] h-44 top-0 right-0";
    case 1:
      return "w-[250px] h-40 bottom-0 right-10";
    case 2:
      return "w-[300px] h-40 top-10 right-20";
    default:
      return "w-32 h-32";
  }
};
</script>

<style scoped>
@keyframes moveAndPulse {
  0%,
  100% {
    transform: translate(0, 0) scale(1);
    opacity: 0.9;
  }
  50% {
    transform: translate(0, 0) scale(1.3);
    opacity: 1;
  }
}

.animate-image {
  animation: moveAndPulse 6s ease-in-out infinite;
}
</style>
