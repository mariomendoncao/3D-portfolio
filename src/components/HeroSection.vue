<script setup></script>

<template>
  <section
    class="min-h-screen flex items-center justify-center bg-transparent text-white text-6xl">
    <div class="max-w-4xl">
      <h1
        class="text-8xl font-extrabold mb-4 animate-fade-in font-terminal text-terminal typing-effect">
        Olá, sou Mário!
      </h1>
      <p class="text-xl text-gray-300 mb-6 animate-fade-in">
        Desenvolvedor(a) Web | Transformando ideias em código
      </p>

      <!-- <div class="flex justify-center gap-4">
        <button
          class="px-6 py-3 text-lg font-semibold bg-amber-500 hover:bg-amber-600 transition rounded-lg shadow-md">
          Veja meus projetos
        </button>
        <button
          class="px-6 py-3 text-lg font-semibold border border-amber-500 hover:bg-amber-500 hover:text-white transition rounded-lg shadow-md">
          Entre em contato
        </button>
      </div> -->
    </div>
  </section>
</template>

<style>
@keyframes fade-in {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-in {
  animation: fade-in 0.8s ease-out forwards;
  animation-delay: 0.5s;
}

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
