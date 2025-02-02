<!-- Background3D.vue -->
<script setup lang="ts">
import { TresCanvas, useRenderLoop } from "@tresjs/core";
import { ref, watch, defineProps } from "vue";
import ComputerModel from "./ComputerModel.vue";
// import { Stars } from "@tresjs/cientos";

// Recebe o valor de "progress" via prop
const props = defineProps<{ progress: number }>();

// Posição inicial do modelo
const initialX = 0;
const initialY = -120;
const initialZ = -60;

// Definição de movimento do modelo conforme o scroll
const moveX = -60;
const moveY = 0;
const moveZ = -220;

// Estado reativo da posição e rotação do modelo
const modelPosition = ref([initialX, initialY, initialZ]);
const modelRotation = ref([0, 0, 0]); // Rotação inicial

// Atualiza a posição do modelo conforme o valor de progress
watch(
  () => props.progress,
  (newVal, oldVal) => {
    // Se o progresso diminuir, reinicia a posição/rotação
    if (newVal < oldVal) {
      modelPosition.value = [initialX, initialY, initialZ];
      modelRotation.value = [0, 0, 0];
    }
    modelPosition.value = [
      initialX + newVal * moveX,
      initialY + newVal * moveY,
      initialZ + newVal * moveZ,
    ];
  }
);

// Controle de oscilação da rotação quando o scroll está no final (progress >= 1)
const rotationDirection = ref(1);
const rotationSpeed = 0.001;
const maxRotation = 45 * (Math.PI / 180);

const { onLoop } = useRenderLoop();
onLoop(() => {
  if (props.progress >= 1) {
    if (modelRotation.value[1] >= maxRotation) {
      rotationDirection.value = -1;
    } else if (modelRotation.value[1] <= -maxRotation) {
      rotationDirection.value = 1;
    }
    modelRotation.value = [
      modelRotation.value[0],
      modelRotation.value[1] + rotationDirection.value * rotationSpeed,
      modelRotation.value[2],
    ];
  }
});
</script>

<template>
  <div class="canvas-container">
    <TresCanvas>
      <!-- Câmera fixa -->
      <TresPerspectiveCamera :position="[0, 1, 5]" :look-at="[0, 0, 0]" />

      <!-- Modelo 3D -->
      <Suspense>
        <ComputerModel :position="modelPosition" :rotation="modelRotation" />
      </Suspense>

      <!-- Iluminação -->
      <TresDirectionalLight :intensity="2" :position="[3, 3, 3]" />
      <TresAmbientLight :intensity="0.7" />
      <!-- <Stars /> -->
    </TresCanvas>
  </div>
</template>

<style>
.canvas-container {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow: hidden; /* Garante que qualquer parte excedente seja ocultada */
}

.canvas-container::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: url("/blury.svg") no-repeat center center;
  background-size: cover;
  filter: blur(16px);
  transform: scale(
    1.1
  ); /* Aumenta um pouco o pseudo-elemento para esconder a borda borrada */
  z-index: -1;
}
</style>
