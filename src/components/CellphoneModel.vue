<script setup lang="ts">
import { useGLTF } from "@tresjs/cientos";

// const path =
//   "https://raw.githubusercontent.com/" +
//   "Tresjs/assets/main/models/gltf/blender-cube.glb";

const path = "/cellphone.glb";
const { scene } = await useGLTF(path, { draco: true });
</script>

<template>
  <primitive :object="scene" />
</template>
