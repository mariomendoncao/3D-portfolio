<template>
  <div ref="canvasContainer" class="canvas-container">
    <!-- Flash effect overlay -->
    <div 
      ref="flashOverlay" 
      class="flash-overlay"
      :class="{ 'flash-active': isFlashing }">
    </div>
    
    <!-- WhatsApp style notification -->
    <div 
      class="notification-popup"
      :class="{ 'notification-active': showNotification }">
      <div class="notification-content">
        <div class="notification-header">
          <div class="notification-avatar">
            📱
          </div>
          <div class="notification-info">
            <div class="notification-sender">Futuro</div>
            <div class="notification-time">agora</div>
          </div>
        </div>
        <div class="notification-message">
          <span class="typing-text" :class="{ 'typing-active': showNotification }">
            Não fique preso no passado! 🚀
          </span>
          <span class="typing-cursor" :class="{ 'cursor-active': showNotification }">|</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted, watch, defineProps } from "vue";
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader.js";

const props = defineProps<{ progress: number }>();

// Refs
const canvasContainer = ref<HTMLDivElement>();
const flashOverlay = ref<HTMLDivElement>();
const isFlashing = ref(false);
const showNotification = ref(false);

// Three.js objects
let scene: THREE.Scene;
let camera: THREE.PerspectiveCamera;
let renderer: THREE.WebGLRenderer;
let computerModel: THREE.Group;
let cellphoneModel: THREE.Group;
let animationId: number;

// Iframe texture variables
let iframeTexture: THREE.Texture;
let screenMesh: THREE.Mesh;
let iframeElement: HTMLIFrameElement;
let canvasElement: HTMLCanvasElement;
let canvasContext: CanvasRenderingContext2D;

// Animation variables
const initialPosition = { x: 0, y: -120, z: -40 };
const moveOffset = { x: -60, y: 0, z: -170 };
let rotationDirection = 1;
const rotationSpeed = 0.01;

// Mouse interaction variables
let mouseX = 0;
let mouseY = 0;
let isMouseOverCellphone = false;
let targetRotationX = 0;
let targetRotationY = 0;
let currentRotationX = 0;
let currentRotationY = 0;

// Transition variables
let showCellphone = false;
let isTransitioning = false;

// Create iframe texture with YouTube video
const createIframeTexture = () => {
  // Create canvas element
  canvasElement = document.createElement('canvas');
  canvasElement.width = 512;
  canvasElement.height = 512;
  canvasContext = canvasElement.getContext('2d')!;
  
  // Create iframe for YouTube video
  iframeElement = document.createElement('iframe');
  iframeElement.width = '512';
  iframeElement.height = '512';
  iframeElement.src = 'https://www.youtube.com/embed/dQw4w9WgXcQ?autoplay=1&mute=1&loop=1&playlist=dQw4w9WgXcQ&controls=0&showinfo=0&rel=0&modestbranding=1';
  iframeElement.frameBorder = '0';
  iframeElement.allow = 'accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture';
  iframeElement.allowFullscreen = true;
  iframeElement.style.position = 'absolute';
  iframeElement.style.top = '-9999px';
  iframeElement.style.left = '-9999px';
  iframeElement.style.visibility = 'hidden';
  
  // Add iframe to document (hidden)
  document.body.appendChild(iframeElement);
  
  // Note: Due to CORS restrictions, we can't directly capture iframe content
  // Instead, we'll create a fallback with canvas content that simulates a video
  
  // Create animated stars content on canvas
  const createAnimatedContent = () => {
    // Clear canvas with dark space background
    const gradient = canvasContext.createRadialGradient(256, 256, 0, 256, 256, 400);
    gradient.addColorStop(0, '#001133');
    gradient.addColorStop(1, '#000000');
    canvasContext.fillStyle = gradient;
    canvasContext.fillRect(0, 0, 512, 512);
    
    const time = Date.now() * 0.0005; // Very slow movement
    
    // Gentle floating stars
    for (let i = 0; i < 30; i++) {
      const x = (Math.sin(time * 0.1 + i) * 180) + 256;
      const y = (Math.cos(time * 0.08 + i * 1.3) * 180) + 256;
      const size = Math.sin(time * 0.5 + i) * 1.5 + 2.5;
      const opacity = Math.sin(time * 0.3 + i) * 0.4 + 0.6;
      
      // Star color varies gently from white to light blue
      const hue = 220 + Math.sin(time * 0.2 + i) * 40;
      canvasContext.fillStyle = `hsla(${hue}, 60%, 85%, ${opacity})`;
      
      // Draw simple star shape
      canvasContext.save();
      canvasContext.translate(x, y);
      canvasContext.rotate(time * 0.1 + i);
      canvasContext.beginPath();
      for (let j = 0; j < 5; j++) {
        const angle = (j * Math.PI * 2) / 5;
        const radius = j % 2 === 0 ? size : size / 2;
        const px = Math.cos(angle) * radius;
        const py = Math.sin(angle) * radius;
        if (j === 0) canvasContext.moveTo(px, py);
        else canvasContext.lineTo(px, py);
      }
      canvasContext.closePath();
      canvasContext.fill();
      canvasContext.restore();
    }
  };
  
  // Create initial content
  createAnimatedContent();
  
  // Create texture from canvas
  iframeTexture = new THREE.CanvasTexture(canvasElement);
  iframeTexture.minFilter = THREE.LinearFilter;
  iframeTexture.magFilter = THREE.LinearFilter;
  iframeTexture.generateMipmaps = false;
  
  // Keep texture settings simple
  iframeTexture.wrapS = THREE.ClampToEdgeWrapping;
  iframeTexture.wrapT = THREE.ClampToEdgeWrapping;
  
  // Animate the canvas content
  const animateCanvas = () => {
    createAnimatedContent();
    iframeTexture.needsUpdate = true;
    requestAnimationFrame(animateCanvas);
  };
  
  // Start animation when cellphone is visible
  setTimeout(() => {
    animateCanvas();
    console.log('🎥 Animated canvas content started');
  }, 1000);
  
  console.log('⭐ Gentle stars animation texture created');
  return iframeTexture;
};

// Initialize Three.js scene
const initThreeJS = () => {
  if (!canvasContainer.value) return;

  const width = window.innerWidth;
  const height = window.innerHeight;

  // Scene
  scene = new THREE.Scene();

  // Camera
  camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000);
  camera.position.set(0, 1, 5);
  camera.lookAt(0, 0, 0);

  // Renderer
  renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
  renderer.setSize(width, height);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.setClearColor(0x000000, 0);
  canvasContainer.value.appendChild(renderer.domElement);

  // Lights
  const directionalLight = new THREE.DirectionalLight(0xffffff, 2);
  directionalLight.position.set(3, 3, 3);
  scene.add(directionalLight);

  const ambientLight = new THREE.AmbientLight(0xffffff, 0.7);
  scene.add(ambientLight);

  // Add fallback wireframe while model loads - exact same as TresJS
  const geometry = new THREE.BoxGeometry(50, 50, 50);
  const material = new THREE.MeshBasicMaterial({
    color: 0x4f46e5,
    wireframe: true,
    transparent: true,
    opacity: 0.5,
  });
  const fallbackCube = new THREE.Mesh(geometry, material);
  fallbackCube.position.set(0, -120, -60); // Same as TresJS fallback position
  fallbackCube.name = "fallback";
  scene.add(fallbackCube);

  // Load models
  loadModels();

  // Start animation
  animate();

  // Handle resize and mouse events
  window.addEventListener("resize", onWindowResize);
  window.addEventListener("mousemove", onMouseMove);
};

// Load both models
const loadModels = () => {
  const loader = new GLTFLoader();

  console.log("Loading models...");

  // Load computer model
  loader.load(
    "/computer_model.glb",
    (gltf) => {
      computerModel = gltf.scene;
      computerModel.position.set(
        initialPosition.x,
        initialPosition.y,
        initialPosition.z
      );
      scene.add(computerModel);

      // Remove fallback
      const fallback = scene.getObjectByName("fallback");
      if (fallback) {
        scene.remove(fallback);
      }

      console.log("Computer model loaded successfully");
    },
    (progress) => {
      console.log(
        "Loading computer model:",
        Math.round((progress.loaded / progress.total) * 100) + "%"
      );
    },
    (error) => {
      console.error("Error loading computer model:", error);
    }
  );

  // Load cellphone model
  loader.load(
    "/cellphone.glb",
    (gltf) => {
      cellphoneModel = gltf.scene;
      cellphoneModel.position.set(
        initialPosition.x,
        initialPosition.y,
        initialPosition.z
      );
      cellphoneModel.visible = false; // Initially hidden
      scene.add(cellphoneModel);

      // Analyze cellphone model structure for screen identification
      console.log("=== DETAILED CELLPHONE ANALYSIS ===");
      
      let allMeshes = [];
      
      cellphoneModel.traverse((child) => {
        if (child.isMesh) {
          const mesh = child;
          const material = mesh.material;
          const geometry = mesh.geometry;
          
          // Calculate bounding box if not available
          if (!geometry.boundingBox) {
            geometry.computeBoundingBox();
          }
          
          const box = geometry.boundingBox;
          let meshInfo = {
            name: mesh.name,
            vertexCount: geometry.attributes.position ? geometry.attributes.position.count : 0,
            material: {
              type: material.type,
              color: material.color ? {
                r: material.color.r,
                g: material.color.g,
                b: material.color.b
              } : null,
              metalness: material.metalness,
              roughness: material.roughness
            }
          };
          
          if (box) {
            const width = Math.abs(box.max.x - box.min.x);
            const height = Math.abs(box.max.y - box.min.y);
            const depth = Math.abs(box.max.z - box.min.z);
            
            meshInfo.dimensions = { width, height, depth };
            meshInfo.area = width * height;
            meshInfo.aspectRatio = width / height;
          }
          
          allMeshes.push({ mesh, info: meshInfo });
          console.log("📱 Mesh found:", meshInfo);
        }
      });
      
      console.log("📋 All meshes found:", allMeshes.length);
      allMeshes.forEach((item, index) => {
        console.log(`  ${index}: ${item.info.name} | Area: ${item.info.area?.toFixed(2)} | Color: R:${item.info.material.color?.r?.toFixed(2)} G:${item.info.material.color?.g?.toFixed(2)} B:${item.info.material.color?.b?.toFixed(2)}`);
      });
      
      // Try to find screen mesh by name patterns first
      let screenCandidates = allMeshes.filter(item => {
        const name = item.info.name.toLowerCase();
        return name.includes('screen') || 
               name.includes('display') || 
               name.includes('lcd') || 
               name.includes('glass') ||
               name.includes('front');
      });
      
      if (screenCandidates.length === 0) {
        // If no name-based candidates, look for the darkest/black material
        screenCandidates = allMeshes.filter(item => {
          const material = item.info.material;
          if (material.color) {
            const sum = material.color.r + material.color.g + material.color.b;
            return sum < 0.5; // Dark materials
          }
          return false;
        });
      }
      
      if (screenCandidates.length === 0) {
        // Fallback: use the flattest mesh with reasonable size
        screenCandidates = allMeshes.filter(item => {
          if (item.info.dimensions) {
            const { width, height, depth } = item.info.dimensions;
            const isFlat = depth < Math.min(width, height) * 0.2;
            const isReasonableSize = item.info.area > 0.1 && item.info.area < 100;
            return isFlat && isReasonableSize;
          }
          return false;
        });
      }
      
      console.log("🎯 Screen candidates found:", screenCandidates.length);
      
      if (screenCandidates.length > 0) {
        console.log("🎯 Screen candidates details:");
        screenCandidates.forEach((candidate, index) => {
          console.log(`  ${index}: ${candidate.info.name} | Area: ${candidate.info.area?.toFixed(2)} | Dims: ${JSON.stringify(candidate.info.dimensions)}`);
        });
        
        // Look for the flattest mesh with reasonable area (likely the main screen)
        const veryFlatMeshes = screenCandidates.filter(item => {
          if (item.info.dimensions) {
            const { depth } = item.info.dimensions;
            return depth < 0.01; // Very flat meshes
          }
          return false;
        });
        
        console.log("📱 Very flat meshes found:", veryFlatMeshes.length);
        veryFlatMeshes.forEach((mesh, index) => {
          console.log(`  ${index}: ${mesh.info.name} | Area: ${mesh.info.area?.toFixed(2)} | Depth: ${mesh.info.dimensions?.depth}`);
        });
        
        let bestCandidate;
        if (veryFlatMeshes.length > 0) {
          // Among flat meshes, choose the one with largest area (main screen)
          veryFlatMeshes.sort((a, b) => (b.info.area || 0) - (a.info.area || 0));
          bestCandidate = veryFlatMeshes[0];
          console.log("📱 Selected largest flat mesh as screen candidate");
        } else {
          // Fallback to largest area overall
          screenCandidates.sort((a, b) => (b.info.area || 0) - (a.info.area || 0));
          bestCandidate = screenCandidates[0];
          console.log("📱 Using fallback selection");
        }
        
        screenMesh = bestCandidate.mesh;
        
        console.log("🖥️ BEST SCREEN CANDIDATE:");
        console.log("  - Name:", bestCandidate.info.name);
        console.log("  - Dimensions:", bestCandidate.info.dimensions);
        console.log("  - Area:", bestCandidate.info.area);
        console.log("  - Material color:", bestCandidate.info.material.color);
        console.log("📱 Applying iframe texture to:", screenMesh.name);
        
        // Create and apply iframe texture
        const texture = createIframeTexture();
        
        // Store original material for potential restoration
        screenMesh.userData.originalMaterial = screenMesh.material.clone();
        
        // Create new material with iframe texture
        const iframeMaterial = new THREE.MeshBasicMaterial({
          map: texture,
          toneMapped: false,
          transparent: true,
          side: THREE.FrontSide
        });
        
        // Apply iframe material to screen
        screenMesh.material = iframeMaterial;
        
        console.log("✅ Iframe texture successfully applied to cellphone screen!");
        
      } else {
        console.log("❌ No clear screen mesh identified. Listing all meshes for manual selection:");
        allMeshes.forEach((item, index) => {
          console.log(`${index}: ${item.info.name} - Area: ${item.info.area} - Dims: ${JSON.stringify(item.info.dimensions)}`);
        });
        
        // Fallback: use the first mesh if available
        if (allMeshes.length > 0) {
          screenMesh = allMeshes[0].mesh;
          console.log("⚠️ Using first mesh as fallback:", allMeshes[0].info.name);
          
          const texture = createImageTexture();
          screenMesh.userData.originalMaterial = screenMesh.material.clone();
          const imageMaterial = new THREE.MeshBasicMaterial({
            map: texture,
            toneMapped: false
          });
          screenMesh.material = imageMaterial;
        }
      }

      console.log("Cellphone model loaded successfully");
    },
    (progress) => {
      console.log(
        "Loading cellphone model:",
        Math.round((progress.loaded / progress.total) * 100) + "%"
      );
    },
    (error) => {
      console.error("Error loading cellphone model:", error);
    }
  );
};

// Mouse move handler
const onMouseMove = (event: MouseEvent) => {
  if (!canvasContainer.value) return;
  
  const rect = canvasContainer.value.getBoundingClientRect();
  mouseX = ((event.clientX - rect.left) / rect.width) * 2 - 1;
  mouseY = -((event.clientY - rect.top) / rect.height) * 2 + 1;
  
  // Check if mouse is over cellphone area (rough approximation)
  const distance = Math.sqrt(mouseX * mouseX + mouseY * mouseY);
  isMouseOverCellphone = distance < 0.8 && props.progress >= 1 && showCellphone;
  
  if (isMouseOverCellphone) {
    // Convert mouse position to rotation angles (limited range)
    targetRotationX = mouseY * 0.3; // Max 17 degrees up/down
    targetRotationY = mouseX * 0.5; // Max 29 degrees left/right
  }
};

// Animation loop
const animate = () => {
  animationId = requestAnimationFrame(animate);

  if (props.progress >= 1) {
    const activeModel = showCellphone ? cellphoneModel : computerModel;
    
    if (activeModel) {
      if (isMouseOverCellphone && showCellphone) {
        // Smooth interpolation towards target rotation
        const lerpFactor = 0.05;
        currentRotationX += (targetRotationX - currentRotationX) * lerpFactor;
        currentRotationY += (targetRotationY - currentRotationY) * lerpFactor;
        
        activeModel.rotation.x = currentRotationX;
        activeModel.rotation.y = currentRotationY;
      } else {
        // Normal continuous rotation when not hovering
        activeModel.rotation.y += rotationSpeed;
        activeModel.rotation.x = 0;
        currentRotationX = 0;
        currentRotationY = activeModel.rotation.y;
      }
    }
  }

  renderer.render(scene, camera);
};

// Handle window resize
const onWindowResize = () => {
  if (!camera || !renderer) return;

  const width = window.innerWidth;
  const height = window.innerHeight;

  camera.aspect = width / height;
  camera.updateProjectionMatrix();
  renderer.setSize(width, height);
};

// Update model position based on scroll
watch(
  () => props.progress,
  (newVal, oldVal) => {
    // Debug logs (reduced)
    if (Math.abs(newVal - 1.0) < 0.01 || Math.abs(newVal - 0.99) < 0.01) {
      console.log(`Progress: ${newVal.toFixed(3)}, showCellphone: ${showCellphone}, isTransitioning: ${isTransitioning}`);
    }
    // Only reset positions on scroll back, but don't change model visibility
    // Model visibility will be handled by the transition logic below
    if (newVal < oldVal) {
      if (computerModel) {
        computerModel.position.set(
          initialPosition.x,
          initialPosition.y,
          initialPosition.z
        );
      }
      
      if (cellphoneModel) {
        cellphoneModel.position.set(
          initialPosition.x,
          initialPosition.y,
          initialPosition.z
        );
      }
    }

    // Calculate new position
    const newX = initialPosition.x + newVal * moveOffset.x;
    const newY = initialPosition.y + newVal * moveOffset.y;
    const newZ = initialPosition.z + newVal * moveOffset.z;

    // Update active model positions
    if (computerModel) {
      computerModel.position.set(newX, newY, newZ);
    }
    if (cellphoneModel) {
      cellphoneModel.position.set(newX, newY, newZ);
    }

    // Simple but smooth transition at 100% progress
    if (newVal >= 1.0 && !showCellphone && !isTransitioning) {
      console.log('Iniciando transição direta - computador para celular');
      showCellphone = true;
      isTransitioning = true;
      
      // Quick smooth spin and swap
      if (computerModel && cellphoneModel) {
        const spinDuration = 800;
        const startTime = Date.now();
        const initialRotation = computerModel.rotation.y;
        
        const spinAndSwap = () => {
          const elapsed = Date.now() - startTime;
          const progress = Math.min(elapsed / spinDuration, 1);
          
          // Smooth easing for entire animation
          const easeInOut = progress < 0.5 
            ? 2 * progress * progress 
            : 1 - Math.pow(-2 * progress + 2, 2) / 2;
            
          if (progress < 0.5) {
            // Computer spins smoothly 90 degrees (quarter rotation)
            computerModel.rotation.y = initialRotation + (Math.PI * 0.5 * easeInOut);
          } else {
            // At 50%, swap models with flash effect
            if (computerModel.visible) {
              // Trigger flash effect
              isFlashing.value = true;
              
              computerModel.visible = false;
              cellphoneModel.visible = true;
              // Start cellphone from where computer left off
              cellphoneModel.rotation.y = initialRotation + (Math.PI * 0.5);
              
              // Remove glitch effect after animation completes
              setTimeout(() => {
                isFlashing.value = false;
              }, 300);
            }
            
            // Cellphone continues rotating smoothly for another 90 degrees
            const cellphoneProgress = (progress - 0.5) / 0.5; // 0 to 1 in second half
            const cellphoneEasing = cellphoneProgress < 0.5 
              ? 2 * cellphoneProgress * cellphoneProgress 
              : 1 - Math.pow(-2 * cellphoneProgress + 2, 2) / 2;
            cellphoneModel.rotation.y = (initialRotation + Math.PI * 0.5) + (Math.PI * 0.5 * cellphoneEasing);
          }
          
          if (progress < 1) {
            requestAnimationFrame(spinAndSwap);
          } else {
            // Clean up
            cellphoneModel.rotation.y = 0;
            computerModel.rotation.y = 0;
            isTransitioning = false;
            
            // Show notification after transition completes
            setTimeout(() => {
              showNotification.value = true;
              
              // Iframe content is now visible on cellphone screen
              if (screenMesh) {
                console.log("🎥 Iframe content is now visible on cellphone screen");
              }
            }, 500);
          }
        };
        
        spinAndSwap();
      }
    } else if (newVal < 1.0 && showCellphone && !isTransitioning) {
      console.log('Iniciando transição reversa - celular para computador');
      showCellphone = false;
      isTransitioning = true;
      showNotification.value = false; // Hide notification on reverse transition
      
      // Iframe content remains on screen (continues animating)
      console.log("🎥 Returning to computer - iframe content continues on cellphone");
      
      // Reverse: cellphone to computer
      if (computerModel && cellphoneModel) {
        console.log('Modelos encontrados, iniciando animação reversa');
        const spinDuration = 800;
        const startTime = Date.now();
        const initialRotation = cellphoneModel.rotation.y;
        
        const reverseSpinAndSwap = () => {
          const elapsed = Date.now() - startTime;
          const progress = Math.min(elapsed / spinDuration, 1);
          
          // Smooth easing for entire reverse animation
          const easeInOut = progress < 0.5 
            ? 2 * progress * progress 
            : 1 - Math.pow(-2 * progress + 2, 2) / 2;
            
          if (progress < 0.5) {
            // Cellphone spins smoothly 90 degrees (quarter rotation)
            cellphoneModel.rotation.y = initialRotation + (Math.PI * 0.5 * easeInOut);
          } else {
            // At 50%, swap models with flash effect
            if (cellphoneModel.visible) {
              console.log('Fazendo a troca: celular -> computador');
              // Trigger flash effect
              isFlashing.value = true;
              
              cellphoneModel.visible = false;
              computerModel.visible = true;
              // Computer appears stationary (no rotation)
              computerModel.rotation.y = 0;
              
              // Remove glitch effect after animation completes
              setTimeout(() => {
                isFlashing.value = false;
              }, 300);
            }
            
            // No additional rotation for computer - it stays stationary
          }
          
          if (progress < 1) {
            requestAnimationFrame(reverseSpinAndSwap);
          } else {
            console.log('Transição reversa completa');
            // Clean up
            computerModel.rotation.y = 0;
            cellphoneModel.rotation.y = 0;
            isTransitioning = false;
          }
        };
        
        reverseSpinAndSwap();
      } else {
        console.log('Modelos não encontrados');
        isTransitioning = false;
      }
    }
  }
);

// Lifecycle
onMounted(() => {
  // Small delay to ensure DOM is ready
  setTimeout(() => {
    initThreeJS();
  }, 50);
});

onUnmounted(() => {
  if (animationId) {
    cancelAnimationFrame(animationId);
  }
  if (renderer) {
    renderer.dispose();
  }
  if (scene) {
    scene.clear();
  }
  window.removeEventListener("resize", onWindowResize);
  window.removeEventListener("mousemove", onMouseMove);
});
</script>

<style scoped>
.canvas-container {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow: hidden;
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
  transform: scale(1.1);
  z-index: -1;
}

.canvas-container canvas {
  display: block;
}

/* Digital glitch effect overlay */
.flash-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
  pointer-events: none;
  z-index: 10;
  background: 
    /* Pixel grid pattern */
    radial-gradient(circle at 25% 25%, #00ffff 1px, transparent 1px),
    radial-gradient(circle at 75% 25%, #ff00ff 1px, transparent 1px),
    radial-gradient(circle at 25% 75%, #ffff00 1px, transparent 1px),
    radial-gradient(circle at 75% 75%, #00ff00 1px, transparent 1px),
    /* Digital scan lines */
    repeating-linear-gradient(
      0deg,
      transparent,
      transparent 2px,
      rgba(0, 255, 255, 0.1) 2px,
      rgba(0, 255, 255, 0.1) 4px
    ),
    /* Glitch bars */
    linear-gradient(
      90deg,
      transparent 0%,
      rgba(255, 0, 255, 0.8) 25%,
      rgba(0, 255, 255, 0.8) 50%,
      rgba(255, 255, 0, 0.8) 75%,
      transparent 100%
    );
  background-size: 20px 20px, 20px 20px, 20px 20px, 20px 20px, 100% 100%, 100% 100%;
}

.flash-overlay.flash-active {
  opacity: 1;
  animation: digitalGlitch 0.3s ease-out;
}

@keyframes digitalGlitch {
  0% {
    opacity: 0;
    transform: scale(1) scaleX(1);
    filter: hue-rotate(0deg) contrast(1);
  }
  15% {
    opacity: 0.8;
    transform: scale(1.02) scaleX(0.98);
    filter: hue-rotate(90deg) contrast(1.5);
    background-position: 0px 0px, 5px 5px, -3px 8px, 7px -2px, 0% 0%, 0% 0%;
  }
  30% {
    opacity: 1;
    transform: scale(0.98) scaleX(1.03);
    filter: hue-rotate(180deg) contrast(2);
    background-position: 3px -2px, -8px 3px, 5px -5px, -7px 9px, 0% 0%, 0% 0%;
  }
  45% {
    opacity: 0.9;
    transform: scale(1.01) scaleX(0.99);
    filter: hue-rotate(270deg) contrast(1.8);
    background-position: -5px 7px, 9px -4px, -3px 6px, 8px -8px, 0% 0%, 0% 0%;
  }
  60% {
    opacity: 0.7;
    transform: scale(0.99) scaleX(1.02);
    filter: hue-rotate(360deg) contrast(1.2);
    background-position: 7px 3px, -6px -7px, 9px 2px, -5px 5px, 0% 0%, 0% 0%;
  }
  80% {
    opacity: 0.4;
    transform: scale(1) scaleX(1);
    filter: hue-rotate(45deg) contrast(1);
    background-position: -2px -2px, 2px 2px, -1px 1px, 1px -1px, 0% 0%, 0% 0%;
  }
  100% {
    opacity: 0;
    transform: scale(1) scaleX(1);
    filter: hue-rotate(0deg) contrast(1);
    background-position: 0px 0px, 0px 0px, 0px 0px, 0px 0px, 0% 0%, 0% 0%;
  }
}

/* Top positioned WhatsApp/Instagram style notification */
.notification-popup {
  position: fixed;
  top: 20%;
  left: 50%;
  transform: translate(-50%, -50%) scale(0.8);
  max-width: 450px;
  width: 90%;
  opacity: 0;
  transition: all 0.5s cubic-bezier(0.25, 0.46, 0.45, 0.94);
  z-index: 1000;
  pointer-events: none;
}

.notification-popup.notification-active {
  opacity: 1;
  transform: translate(-50%, -50%) scale(1);
}

.notification-content {
  background: rgba(0, 0, 0, 0.90);
  backdrop-filter: blur(25px);
  border: 1px solid rgba(255, 255, 255, 0.25);
  border-radius: 24px;
  padding: 24px 28px;
  box-shadow: 
    0 25px 60px rgba(0, 0, 0, 0.4),
    0 0 0 1px rgba(255, 255, 255, 0.08),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
}

.notification-header {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 16px;
}

.notification-avatar {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background: linear-gradient(135deg, #00d4ff, #0099ff);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  box-shadow: 0 4px 16px rgba(0, 153, 255, 0.4);
}

.notification-info {
  flex: 1;
}

.notification-sender {
  font-size: 18px;
  font-weight: 700;
  color: white;
  margin-bottom: 4px;
}

.notification-time {
  font-size: 14px;
  color: rgba(255, 255, 255, 0.7);
}

.notification-message {
  font-size: 18px;
  color: rgba(255, 255, 255, 0.95);
  line-height: 1.5;
  font-weight: 500;
  text-align: center;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Typing effect */
.typing-text {
  overflow: hidden;
  white-space: nowrap;
  width: 0;
  animation: none;
}

.typing-text.typing-active {
  animation: typewriter 2.5s steps(30, end) 0.5s forwards;
}

.typing-cursor {
  opacity: 0;
  font-weight: 100;
  color: rgba(255, 255, 255, 0.8);
  margin-left: 2px;
}

.typing-cursor.cursor-active {
  animation: blink 1s infinite step-end 0.5s;
}

@keyframes typewriter {
  0% {
    width: 0;
  }
  100% {
    width: 100%;
  }
}

@keyframes blink {
  0%, 50% {
    opacity: 1;
  }
  51%, 100% {
    opacity: 0;
  }
}

/* Mobile responsive */
@media (max-width: 768px) {
  .notification-popup {
    max-width: 380px;
    width: 85%;
  }
  
  .notification-content {
    padding: 20px 24px;
  }
  
  .notification-avatar {
    width: 40px;
    height: 40px;
    font-size: 20px;
  }
  
  .notification-sender {
    font-size: 16px;
  }
  
  .notification-time {
    font-size: 12px;
  }
  
  .notification-message {
    font-size: 16px;
  }
}
</style>
