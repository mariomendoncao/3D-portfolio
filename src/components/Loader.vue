<template>
  <div v-if="isLoading" class="fixed inset-0 z-50 flex items-center justify-center bg-black/80 backdrop-blur-sm">
    <div class="text-center">
      <!-- Spinner 3D -->
      <div class="relative w-24 h-24 mx-auto mb-6">
        <div class="absolute inset-0 border-4 border-blue-200 rounded-full opacity-25"></div>
        <div class="absolute inset-0 border-4 border-blue-500 rounded-full border-t-transparent animate-spin"></div>
        <div class="absolute inset-2 border-4 border-purple-500 rounded-full border-r-transparent animate-spin-reverse"></div>
      </div>
      
      <!-- Texto de loading -->
      <h2 class="text-2xl font-bold text-white mb-2">{{ loadingText }}</h2>
      <p class="text-gray-300">{{ subText }}</p>
      
      <!-- Barra de progresso -->
      <div v-if="showProgress" class="w-64 h-2 bg-gray-700 rounded-full mt-4 mx-auto overflow-hidden">
        <div 
          class="h-full bg-gradient-to-r from-blue-500 via-purple-500 to-pink-500 transition-all duration-300 ease-out"
          :style="{ width: progress + '%' }"
        ></div>
      </div>
      <p v-if="showProgress" class="text-sm text-gray-400 mt-2">{{ Math.round(progress) }}%</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const props = defineProps({
  isLoading: {
    type: Boolean,
    default: true
  },
  loadingText: {
    type: String,
    default: "Carregando..."
  },
  subText: {
    type: String,
    default: "Preparando uma experiência incrível"
  },
  showProgress: {
    type: Boolean,
    default: true
  },
  duration: {
    type: Number,
    default: 3000
  }
});

const emit = defineEmits(['loaded']);

const progress = ref(0);

onMounted(() => {
  if (props.showProgress) {
    // Simula progresso de carregamento
    const interval = setInterval(() => {
      progress.value += Math.random() * 15;
      
      if (progress.value >= 100) {
        progress.value = 100;
        clearInterval(interval);
        
        // Aguarda um pouco antes de emitir o evento de carregamento completo
        setTimeout(() => {
          emit('loaded');
        }, 500);
      }
    }, props.duration / 20);
  } else {
    // Se não mostrar progresso, apenas aguarda a duração definida
    setTimeout(() => {
      emit('loaded');
    }, props.duration);
  }
});
</script>

<style scoped>
@keyframes spin-reverse {
  from {
    transform: rotate(360deg);
  }
  to {
    transform: rotate(0deg);
  }
}

.animate-spin-reverse {
  animation: spin-reverse 1s linear infinite;
}
</style>
