<template>
  <div ref="modelContainer" class="model-container"></div>
</template>

<script>
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';

export default {
  name: 'ModelViewer',
  mounted() {
    this.init();
    this.addEventListeners();
  },
  beforeDestroy() {
    this.removeEventListeners();
  },
  methods: {
    init() {
      const { innerWidth: maxW, innerHeight: maxH } = window;

      // Set up scene
      this.scene = new THREE.Scene();

      // Set up camera
      this.camera = new THREE.PerspectiveCamera(75, maxW / maxH, 0.1, 1000);
      this.camera.position.set(0, 1, 1.65);

      // Set up renderer
      this.renderer = new THREE.WebGLRenderer({ alpha: true });
      this.updateRendererSize();
      this.renderer.shadowMap.enabled = true;
      this.$refs.modelContainer.appendChild(this.renderer.domElement);

      // Load model
      const loader = new GLTFLoader();
      loader.load('/silent_ash.glb', (glb) => {
        this.model = glb.scene;
        this.model.traverse((child) => {
          if (child.isMesh) {
            //child.castShadow = true;
            child.receiveShadow = true;
          }
        });
        this.scene.add(this.model);
      }, undefined, (error) => {
        console.error('Error loading GLB model:', error);
      });

      // Add lights
      const directionalLight = new THREE.DirectionalLight(0xffffff, 3.5);
      directionalLight.position.set(0, 2, 2);
      directionalLight.castShadow = true;

      const ambientLight = new THREE.AmbientLight(0xffffff, 2);

      this.scene.add(directionalLight, ambientLight);

      // Start animation loop
      this.animate();
    },
    addEventListeners() {
      window.addEventListener('resize', this.updateRendererSize);
      this.$refs.modelContainer.addEventListener('mousedown', this.onMouseDown);
      this.$refs.modelContainer.addEventListener('touchstart', this.onTouchStart);
      this.$refs.modelContainer.addEventListener('mousemove', this.onMouseMove);
      this.$refs.modelContainer.addEventListener('touchmove', this.onTouchMove);
      this.$refs.modelContainer.addEventListener('mouseup', this.onMouseUp);
      this.$refs.modelContainer.addEventListener('touchend', this.onTouchEnd);
    },
    removeEventListeners() {
      window.removeEventListener('resize', this.updateRendererSize);
      this.$refs.modelContainer.removeEventListener('mousedown', this.onMouseDown);
      this.$refs.modelContainer.removeEventListener('touchstart', this.onTouchStart);
      this.$refs.modelContainer.removeEventListener('mousemove', this.onMouseMove);
      this.$refs.modelContainer.removeEventListener('touchmove', this.onTouchMove);
      this.$refs.modelContainer.removeEventListener('mouseup', this.onMouseUp);
      this.$refs.modelContainer.removeEventListener('touchend', this.onTouchEnd);
    },
    updateRendererSize() {
      const { innerWidth: maxW, innerHeight: maxH } = window;
      const dpr = window.devicePixelRatio;

      this.renderer.setSize(maxW * dpr, maxH * dpr, false);
      this.renderer.domElement.style.width = `${maxW}px`;
      this.renderer.domElement.style.height = `${maxH}px`;

      this.camera.aspect = maxW / maxH;
      this.camera.updateProjectionMatrix();
    },
    onMouseDown(event) {
      this.isMouseDown = true;
      this.previousMousePosition = { x: event.clientX, y: event.clientY };
    },
    onMouseMove(event) {
      if (!this.isMouseDown) return;
      const delta = { x: event.clientX - this.previousMousePosition.x, y: event.clientY - this.previousMousePosition.y };
      this.rotateModel(delta);
      this.previousMousePosition = { x: event.clientX, y: event.clientY };
    },
    onMouseUp() {
      this.isMouseDown = false;
    },
    onTouchStart(event) {
      this.isTouching = true;
      const touch = event.touches[0];
      this.previousTouchPosition = { x: touch.clientX, y: touch.clientY };
    },
    onTouchMove(event) {
      if (!this.isTouching) return;
      const touch = event.touches[0];
      const delta = { x: touch.clientX - this.previousTouchPosition.x, y: touch.clientY - this.previousTouchPosition.y };
      this.rotateModel(delta);
      this.previousTouchPosition = { x: touch.clientX, y: touch.clientY };
    },
    onTouchEnd() {
      this.isTouching = false;
    },
    rotateModel(delta) {
      const rotationSpeed = 0.005;
      this.model.rotation.y += delta.x * rotationSpeed;

      const maxVerticalRotation = Math.PI / 6;
      const newRotationX = this.model.rotation.x + delta.y * rotationSpeed;
      this.model.rotation.x = Math.max(-maxVerticalRotation, Math.min(maxVerticalRotation, newRotationX));
    },
    animate() {
      requestAnimationFrame(this.animate);
      this.renderer.render(this.scene, this.camera);
    },
  },
};
</script>

<style scoped>
html, body, #app, .model-container {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.model-container {
  position: relative;
  width: 100%;
  height: 100vh;
}
</style>
