<template>
  <div>
      <div ref="threeContainer" style="width: 100%; height: 100vh;"></div>
  </div>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import { PointLight } from 'three';

import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';

export default {
name: 'ThreeComponent',
mounted() {
  const container = this.$refs.threeContainer;
  if (!container) {
    console.error('Element with ref "threeContainer" not found');
    return;
  }

  const renderer = new THREE.WebGLRenderer();
  renderer.setSize(window.innerWidth, window.innerHeight);
  container.appendChild(renderer.domElement);

  const scene = new THREE.Scene();
  scene.background = new THREE.Color(0xdddddd);

  const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 1, 2000);
  camera.rotation.y = (45 / 100) * Math.PI;
  camera.position.x = 800;
  camera.position.y = 100;
  camera.position.z = 1000;

  const controls = new OrbitControls(camera, renderer.domElement);
  controls.update();

  // Adicionando luzes à cena
  const light1 = new PointLight(0xc4c4c4, 500000);
  light1.position.set(0, 300, 500);
  scene.add(light1);

  const light2 = new PointLight(0xc4c4c4, 500000);
  light2.position.set(500, 100, 0);
  scene.add(light2);

  const light3 = new PointLight(0xc4c4c4, 500000);
  light3.position.set(0, 100, -500);
  scene.add(light3);

  const light4 = new PointLight(0xc4c4c4, 50000);
  light4.position.set(-500, 300, 500);
  scene.add(light4);

 // Carregando o modelo GLTF
  const gltfLoader = new GLTFLoader();
  gltfLoader.load('bee_gltf.glb', (gltf) => {
    const model = gltf.scene;
    model.scale.set(150, 150, 150); // Defina a escala do objeto
    model.position.y = -50;
    scene.add(model);

    if (gltf.animations && gltf.animations.length > 0) {
      console.log('Animações encontradas:', gltf.animations);

      // Adicionando animação
      const mixer = new THREE.AnimationMixer(model);
      gltf.animations.forEach((clip) => {
        mixer.clipAction(clip).play();
      });

      const animate = () => {
        requestAnimationFrame(animate);
        controls.update();
        mixer.update(0.01); // Atualize a animação
        renderer.render(scene, camera);
      };

      animate();
    } else {
      console.log('Nenhuma animação encontrada no modelo.');
    }
  });
  window.addEventListener('resize', onWindowResize, false);
  function onWindowResize() {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  }
}
};
</script>

<style scoped>
div {
  width: 100%;
  height: 100%;
}
</style>
