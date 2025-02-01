<script setup></script>

<template>
  <section class="w-full">
    <div class="max-w-4xl">
      <h1 class="text-5xl font-extrabold mb-4 animate-fade-in">
        Olá, sou <span class="text-amber-400">xxx</span>
      </h1>
      <p class="text-xl text-gray-300 mb-6 animate-fade-in delay-200">
        Desenvolvedor(a) Web | Transformando ideias em código
      </p>

      <div class="flex justify-center gap-4">
        <button
          class="px-6 py-3 text-lg font-semibold bg-amber-500 hover:bg-amber-600 transition rounded-lg shadow-md">
          Veja meus projetos
        </button>
        <button
          class="px-6 py-3 text-lg font-semibold border border-amber-500 hover:bg-amber-500 hover:text-white transition rounded-lg shadow-md">
          Entre em contato
        </button>
      </div>
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
}
</style>
