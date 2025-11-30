<template>
  <BaseLayout
    :progress="progress"
    :minInput="0.03"
    :fullStart="0.18"
    :fullEnd="0.31"
    :maxInput="0.34"
    class="min-h-screen flex items-center justify-start text-white text-4xl flex-col pt-8 md:pt-12 px-4 md:px-6">
    <h1 class="main-title-text">Meus serviços</h1>

    <div
      class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8 w-full max-w-6xl text-center">
      <div
        v-for="skill in skills"
        :key="skill.title"
        class="rounded h-100 w-full p-4 bg-gray-300/20 backdrop-blur-lg">
        <div class="text-6xl mb-4">
          <component :is="skill.icon" />
          <DotLottieVue autoplay loop :src="skill.animation" />
        </div>
        <h3 class="title-text">
          {{ skill.title }}
        </h3>
        <p class="text-white text-base md:text-xl">{{ skill.description }}</p>
      </div>
    </div>
  </BaseLayout>
</template>

<script setup>
import { ref } from "vue";
import BaseLayout from "./BaseLayout.vue";
// import { Code, Brush, Layers } from "lucide-vue-next";
import { DotLottieVue } from "@lottiefiles/dotlottie-vue";

const props = defineProps(["progress"]);

import { skills } from "../data/skills.js";
</script>
