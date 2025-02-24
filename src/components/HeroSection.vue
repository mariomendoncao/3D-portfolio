<script setup>
import { ref, watch, defineProps } from "vue";

// Recebe o valor de "progress" via prop
const props = defineProps(["progress"]);

// define a opacidade do elemento conforme o progresso e diminuita a opacidade conforme o scroll
const opacity = ref(1);
watch(
  () => props.progress,
  (newVal) => {
    opacity.value = 1 - newVal * 15;
  }
);

// Estado reativo que controla a exibição do texto
const showText = ref(false);
setTimeout(() => (showText.value = true), 3000);
</script>

<template>
  <section
    class="relative min-h-screen flex flex-col items-center justify-end pb-64 bg-transparent text-white text-6xl px-6"
    :style="{ opacity: opacity }">
    <!-- Título principal SEM movimentação -->
    <div class="relative">
      <!-- <p>opacidade {{ opacity }}</p> -->
      <h1
        class="text-8xl font-extrabold mb-4 font-terminal text-terminal typing-effect">
        Olá, sou Mário!
      </h1>
    </div>

    <!-- Bloco de texto POSICIONADO ABSOLUTAMENTE para não empurrar o título -->
    <div
      class="text-center max-w-6xl opacity-0 transition-opacity duration-3000 ease-in-out"
      :class="{ 'opacity-100': showText }">
      <h1 class="text-6xl font-extrabold leading-tight">
        Criando experiências
        <span
          class="bg-gradient-to-r from-cyan-400 via-blue-500 to-indigo-600 text-transparent bg-clip-text"
          >digitais</span
        >
        interativas
      </h1>

      <p class="text-2xl text-gray-300 mt-6">
        Sou um desenvolvedor apaixonado por transformar ideias em soluções
        inovadoras. Construo aplicações performáticas e interfaces envolventes
        que encantam usuários.
      </p>
    </div>
  </section>
</template>

<style>
@keyframes typing {
  from {
    width: 0;
  }
  to {
    width: 100%;
  }
}

.typing-effect {
  overflow: hidden;
  white-space: nowrap;
  border-right: 8px solid #00ff00; /* Simula um cursor */
  width: 100%;
  display: inline-block;
  animation: typing 3s steps(20, end) forwards, blinkCursor 0.8s infinite;
}

@keyframes blinkCursor {
  50% {
    border-color: transparent;
  }
}
</style>
