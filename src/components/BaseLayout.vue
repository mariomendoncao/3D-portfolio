<template>
  <section :style="{ opacity: computedOpacity }">
    <!-- Conteúdo passado via slot -->
    <slot />
  </section>
</template>

<script setup>
import { defineProps, computed } from "vue";

const props = defineProps({
  progress: {
    type: Number,
    required: true,
  },
  // Valores que delimitam a opacidade:
  minInput: {
    type: Number,
    default: 0.03,
  },
  fullStart: {
    type: Number,
    default: 0.1,
  },
  fullEnd: {
    type: Number,
    default: 0.2,
  },
  maxInput: {
    type: Number,
    default: 0.25,
  },
  cssClasses: {
    type: String,
    default: "",
  },
});

/**
 * Converte o valor atual para uma escala de opacidade:
 * - Se currentValue <= minInput ou currentValue >= maxInput => opacidade 0
 * - Se currentValue está entre minInput e fullStart => fade in de 0 a 100
 * - Se currentValue está entre fullStart e fullEnd => opacidade total (100)
 * - Se currentValue está entre fullEnd e maxInput => fade out de 100 a 0
 */
const convertValue = (currentValue, minInput, fullStart, fullEnd, maxInput) => {
  if (currentValue <= minInput) return 0;
  if (currentValue >= maxInput) return 0;
  if (currentValue < fullStart) {
    return (((currentValue - minInput) / (fullStart - minInput)) * 100).toFixed(
      0
    );
  }
  if (currentValue <= fullEnd) {
    return 100;
  }
  if (currentValue < maxInput) {
    return (((maxInput - currentValue) / (maxInput - fullEnd)) * 100).toFixed(
      0
    );
  }
};

// Calcula a opacidade (valor entre 0 e 1) usando os valores recebidos via props.
const computedOpacity = computed(() => {
  const conv = parseFloat(
    convertValue(
      props.progress,
      props.minInput,
      props.fullStart,
      props.fullEnd,
      props.maxInput
    )
  );
  return conv / 100;
});
</script>
