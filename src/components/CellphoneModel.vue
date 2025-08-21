<script setup lang="ts">
import { useGLTF } from "@tresjs/cientos";

const props = defineProps<{
  position?: [number, number, number];
  rotation?: [number, number, number];
}>();

const path = "/cellphone.glb";
const { scene } = await useGLTF(path, { draco: true });
</script>

<template>
  <primitive 
    :object="scene" 
    :position="props.position" 
    :rotation="props.rotation" 
  />
</template>
