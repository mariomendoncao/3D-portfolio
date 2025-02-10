<template>
  <!-- Atribuímos um ref ("container") à seção para monitorar sua posição na tela -->
  <section
    ref="container"
    class="min-h-screen flex items-center justify-start text-white text-4xl flex-col">
    <h2 class="text-6xl font-bold text-amber-400 mb-8">Minhas Habilidades</h2>

    <div
      class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8 w-full max-w-6xl text-center">
      <div
        v-for="(skill, index) in skills"
        :key="skill.title"
        class="rounded p-4 bg-gray-500/50 transition-transform transform hover:scale-105"
        :style="getCardStyle(index)">
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
import { ref, onMounted, onUnmounted } from "vue";
import { Code, Brush, Layers } from "lucide-vue-next";

// Ref para o contêiner que vamos monitorar (a seção que contém os cards)
const container = ref(null);

// Variável reativa que armazenará o “progresso” da seção na tela
// Esse valor será 0 quando o contêiner estiver distante do centro da viewport
// e se aproximará de 1 quando o contêiner estiver centralizado.
const progress = ref(0);

/**
 * Atualiza o valor de "progress" com base na posição do contêiner.
 *
 * Utilizamos o centro da seção para determinar o quão “centralizado” ela está.
 * Quando o centro do contêiner coincide com o centro da viewport, progress = 1.
 * Quando está distante (próximo à parte superior ou inferior da viewport), progress tende a 0.
 */
const updateProgress = () => {
  if (container.value) {
    const rect = container.value.getBoundingClientRect();
    const windowHeight = window.innerHeight;
    // Calcula a posição do centro do contêiner
    const center = rect.top + rect.height / 2;
    // Quanto mais próximo do centro da viewport (windowHeight/2), maior o valor
    let p = 1 - (2 * Math.abs(center - windowHeight / 2)) / windowHeight;
    p = Math.min(Math.max(p, 0), 1);
    progress.value = p;
  }
};

// Registra o listener do scroll para atualizar o progresso dinamicamente
onMounted(() => {
  window.addEventListener("scroll", updateProgress);
  updateProgress();
});

onUnmounted(() => {
  window.removeEventListener("scroll", updateProgress);
});

// Lista de habilidades (cards)
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

/**
 * Função que calcula os estilos inline para cada card com base no scroll.
 * Utilizamos o índice do card para criar um pequeno atraso (stagger) na animação.
 *
 * Para cada card:
 * - Calculamos um "localProgress" que é o progresso global (progress)
 *   reduzido de acordo com o atraso do card (index * 0.1).
 * - A opacidade é definida igual ao localProgress.
 * - O card inicia deslocado 50px para a esquerda e se desloca para 0 conforme o progresso aumenta.
 * - A escala varia de 0.8 (inicial) a 1 (final).
 */
const getCardStyle = (index) => {
  const delay = index * 0.1; // atraso para efeito em cascata
  let localProgress = progress.value - delay;
  if (localProgress < 0) localProgress = 0;
  if (localProgress > 1) localProgress = 1;

  const opacity = localProgress;
  const translateX = -50 * (1 - localProgress); // de -50px (inicial) a 0px (final)
  const scale = 0.8 + 0.2 * localProgress; // de 0.8 (inicial) a 1.0 (final)

  return {
    opacity: opacity,
    transform: `translateX(${translateX}px) scale(${scale})`,
  };
};
</script>
