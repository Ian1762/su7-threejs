<script setup lang="ts">
import { reactive, ref, onMounted } from "vue";
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
// import { GLTFLoader } from "../lib/GLTFLoader.js";
import { MeshoptDecoder } from "three/examples/jsm/libs/meshopt_decoder.module.js";

const sceneRef = ref();
let scene: THREE.Scene;
let width: number;
let height: number;
let renderer: THREE.WebGLRenderer;
let camera: THREE.PerspectiveCamera;
let controls: OrbitControls;
const initScene = () => {
  scene = new THREE.Scene();
  width = sceneRef.value.clientWidth;
  height = sceneRef.value.clientHeight;

  renderer = new THREE.WebGLRenderer();
  renderer.setSize(width, height);
  sceneRef.value.appendChild(renderer.domElement);
};
const initCamera = (width: number, height: number) => {
  camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000);
  camera.position.z = 5;
};
const addOrbitControls = () => {
  controls = new OrbitControls(camera, renderer.domElement);
};
const addLight = () => {
  // 环境光
  const ambientLight = new THREE.AmbientLight(0xffffff);
  scene.add(ambientLight);
  // 点光源
  const pointLight = new THREE.PointLight(0x0655fd, 5, 0);
  pointLight.position.set(2, 2, 2);
  scene.add(pointLight);
};
const addAxesHelper = () => {
  const axesHelper = new THREE.AxesHelper(5);
  scene.add(axesHelper);
};
const addMesh = () => {
  const geometry = new THREE.BoxGeometry(1, 1, 1);
  const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
  const cube = new THREE.Mesh(geometry, material);
  scene.add(cube);
};
const addCar = () => {
  // Instantiate a loader
  const loader = new GLTFLoader();

  // 设置MeshoptDecoder
  loader.setMeshoptDecoder(MeshoptDecoder);

  //   const dracoLoader = new DRACOLoader();
  // dracoLoader.setDecoderPath( '/examples/jsm/libs/draco/' );
  // loader.setDRACOLoader( dracoLoader );

  loader.load(
    // resource URL
    "mesh/sm_car.gltf",
    // "/1.0.2/mesh/sm_car.glb",
    // called when the resource is loaded
    function (gltf) {
      scene.add(gltf.scene);

      gltf.animations; // Array<THREE.AnimationClip>
      gltf.scene; // THREE.Group
      gltf.scenes; // Array<THREE.Group>
      gltf.cameras; // Array<THREE.Camera>
      gltf.asset; // Object
    },
    // called while loading is progressing
    function (xhr) {
      console.log((xhr.loaded / xhr.total) * 100 + "% loaded");
    },
    // called when loading has errors
    function (error) {
      console.log("An error happened", error);
    }
  );
};
const animate = () => {
  requestAnimationFrame(animate);

  controls.update();

  renderer.render(scene, camera);
};
onMounted(() => {
  // 初始化场景
  initScene();
  // 初始相机
  initCamera(width, height);
  // 添加轨道控制器
  addOrbitControls();
  // 添加灯光
  addLight();
  // 添加坐标辅助
  addAxesHelper();
  // 添加物体
  // addMesh();
  addCar();
  // 持续动画
  animate();
});
</script>

<template>
  <div class="scene" ref="sceneRef"></div>
</template>

<style lang="scss" scoped>
.scene {
  width: 100vw;
  height: 100vh;
}
</style>
