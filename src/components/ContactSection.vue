<template>
  <BaseLayout
    :progress="progress"
    :minInput="0.75"
    :fullStart="0.8"
    :fullEnd="1.1"
    :maxInput="1.1"
    class="min-h-screen flex items-center justify-end text-white px-6 md:px-12 lg:px-16">
    
    <div class="w-full h-full flex items-center justify-end">
      <!-- Container dos Contatos - Lado Direito -->
      <div class="relative z-10 max-w-md">
        <!-- Painel Principal de Contato -->
        <div class="bg-black/40 backdrop-blur-2xl border border-white/40 rounded-3xl p-8 shadow-2xl shadow-black/60">
          <!-- Cabeçalho -->
          <div class="text-center mb-8">
            <div class="w-20 h-20 bg-gradient-to-br from-blue-500 via-purple-500 to-pink-500 rounded-full mx-auto mb-4 flex items-center justify-center shadow-xl shadow-purple-500/30">
              <div class="w-16 h-16 bg-black/30 backdrop-blur-sm rounded-full flex items-center justify-center border border-white/20">
                <span class="text-3xl font-bold text-white">M</span>
              </div>
            </div>
            <h1 class="text-4xl font-bold text-white mb-3 drop-shadow-lg">Entre em Contato</h1>
            <p class="text-gray-100 text-lg font-medium">Vamos conversar sobre seu projeto</p>
          </div>

          <!-- Cards de Contato -->
          <div class="space-y-4">
            <!-- WhatsApp -->
            <a 
              href="https://wa.me/5511999999999"
              target="_blank"
              rel="noopener noreferrer"
              class="group block bg-white/25 backdrop-blur-xl border border-white/50 rounded-2xl p-6 hover:bg-white/35 hover:border-white/70 transition-all duration-300 hover:scale-[1.03] hover:shadow-2xl hover:shadow-green-500/25">
              <div class="flex items-center space-x-4">
                <div class="bg-gradient-to-br from-green-400 to-emerald-600 p-4 rounded-xl shadow-xl group-hover:shadow-green-400/50 transition-all duration-300">
                  <Phone class="w-7 h-7 text-white drop-shadow-sm" />
                </div>
                <div class="flex-1">
                  <h3 class="text-2xl font-bold text-white mb-1 drop-shadow-sm">WhatsApp</h3>
                  <p class="text-green-200 font-bold text-xl drop-shadow-sm">(11) 99999-9999</p>
                  <p class="text-gray-100 text-sm font-medium">Resposta rápida garantida</p>
                </div>
              </div>
            </a>

            <!-- GitHub -->
            <a 
              href="https://github.com/mariorodrigues"
              target="_blank"
              rel="noopener noreferrer"
              class="group block bg-white/25 backdrop-blur-xl border border-white/50 rounded-2xl p-6 hover:bg-white/35 hover:border-white/70 transition-all duration-300 hover:scale-[1.03] hover:shadow-2xl hover:shadow-purple-500/25">
              <div class="flex items-center space-x-4">
                <div class="bg-gradient-to-br from-purple-500 to-pink-600 p-4 rounded-xl shadow-xl group-hover:shadow-purple-400/50 transition-all duration-300">
                  <Github class="w-7 h-7 text-white drop-shadow-sm" />
                </div>
                <div class="flex-1">
                  <h3 class="text-2xl font-bold text-white mb-1 drop-shadow-sm">GitHub</h3>
                  <p class="text-purple-200 font-bold text-xl drop-shadow-sm">/mariorodrigues</p>
                  <p class="text-gray-100 text-sm font-medium">Veja meus projetos</p>
                </div>
              </div>
            </a>

            <!-- LinkedIn -->
            <a 
              href="https://linkedin.com/in/mariorodrigues"
              target="_blank"
              rel="noopener noreferrer"
              class="group block bg-white/25 backdrop-blur-xl border border-white/50 rounded-2xl p-6 hover:bg-white/35 hover:border-white/70 transition-all duration-300 hover:scale-[1.03] hover:shadow-2xl hover:shadow-blue-500/25">
              <div class="flex items-center space-x-4">
                <div class="bg-gradient-to-br from-blue-600 to-blue-700 p-4 rounded-xl shadow-xl group-hover:shadow-blue-400/50 transition-all duration-300">
                  <Linkedin class="w-7 h-7 text-white drop-shadow-sm" />
                </div>
                <div class="flex-1">
                  <h3 class="text-2xl font-bold text-white mb-1 drop-shadow-sm">LinkedIn</h3>
                  <p class="text-blue-200 font-bold text-xl drop-shadow-sm">/in/mariorodrigues</p>
                  <p class="text-gray-100 text-sm font-medium">Conecte-se comigo</p>
                </div>
              </div>
            </a>
          </div>

          <!-- Call to Action -->
          <div class="mt-8">
            <div class="bg-gradient-to-r from-blue-500/30 via-purple-500/30 to-pink-500/30 backdrop-blur-sm border border-white/40 rounded-2xl p-6 text-center">
              <h4 class="text-white font-bold text-xl mb-2 drop-shadow-sm">Pronto para começar?</h4>
              <p class="text-gray-100 font-medium">Escolha a forma de contato que preferir e vamos transformar sua ideia em realidade!</p>
            </div>
          </div>
        </div>

        <!-- Elementos Decorativos -->
        <div class="absolute -top-6 -right-6 w-12 h-12 bg-gradient-to-br from-blue-400 to-purple-500 rounded-full opacity-60 animate-pulse blur-sm"></div>
        <div class="absolute -bottom-8 -left-8 w-16 h-16 bg-gradient-to-br from-pink-400 to-purple-500 rounded-full opacity-40 animate-pulse delay-300 blur-sm"></div>
        <div class="absolute top-1/2 -left-4 w-6 h-6 bg-gradient-to-br from-green-400 to-blue-500 rounded-full opacity-50 animate-bounce delay-500"></div>
      </div>
    </div>
  </BaseLayout>
</template>

<script setup>
import { ref } from "vue";
import BaseLayout from "./BaseLayout.vue";
import { Phone, Github, Linkedin } from "lucide-vue-next";

const props = defineProps(["progress"]);
</script>