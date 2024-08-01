<script setup lang="ts">
import { reactive, ref, onMounted } from "vue";
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
import { DRACOLoader } from "three/addons/loaders/DRACOLoader.js";
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
  scene.background = new THREE.Color("#ccc"); // 设置场景背景颜色

  renderer = new THREE.WebGLRenderer({ antialias: true }); //antialias 抗锯齿
  renderer.setSize(width, height);
  renderer.setClearColor(new THREE.Color(1, 1, 1)); // 设置画面颜色
  sceneRef.value.appendChild(renderer.domElement);
};
const initCamera = (width: number, height: number) => {
  camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 1000);
  camera.position.set(0, 2, 6); // 设置相机位置
  camera.lookAt(scene.position); // 相机视点
};

const addGrid = () => {
  // 添加网格地面
  let gridHelper = new THREE.GridHelper(10, 10); // 创建一个网格帮助器，参数为网格的宽度和高度
  scene.add(gridHelper);
  gridHelper.material.transparent = true; // 开启网格帮助器的透明度
  gridHelper.material.opacity = 0.5; // 设置网格帮助器的透明度
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

  // 添加灯光
  let light1 = new THREE.DirectionalLight(0xffffff, 1); // 创建一个方向光，参数为光的颜色和强度
  light1.position.set(0, 0, 10);
  scene.add(light1);
  let light2 = new THREE.DirectionalLight(0xffffff, 1);
  light2.position.set(0, 0, -10);
  scene.add(light2);
  let light3 = new THREE.DirectionalLight(0xffffff, 1);
  light3.position.set(10, 0, 0);
  scene.add(light3);
  let light4 = new THREE.DirectionalLight(0xffffff, 1);
  light4.position.set(-10, 0, 0);
  scene.add(light4);
  let light5 = new THREE.DirectionalLight(0xffffff, 1);
  light5.position.set(0, 10, 0);
  scene.add(light5);
  let light6 = new THREE.DirectionalLight(0xffffff, 1);
  light6.position.set(0, -10, 0);
  scene.add(light6);
  let light7 = new THREE.DirectionalLight(0xffffff, 1);
  light7.position.set(10, 10, 10);
  scene.add(light7);
  let light8 = new THREE.DirectionalLight(0xffffff, 1);
  light8.position.set(10, 10, -10);
  scene.add(light8);
  let light9 = new THREE.DirectionalLight(0xffffff, 1);
  light9.position.set(-10, 10, 10);
  scene.add(light9);
  let light10 = new THREE.DirectionalLight(0xffffff, 1);
  light10.position.set(-10, 10, -10);
  scene.add(light10);
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

  const dracoLoader = new DRACOLoader();
  dracoLoader.setDecoderPath("/examples/jsm/libs/draco/");
  loader.setDRACOLoader(dracoLoader);

  loader.load(
    // resource URL
    "mesh/sm_car.gltf",
    // called when the resource is loaded
    function (gltf) {
      let object = gltf.scene; // 获取模型的场景

      let rotate = () => {
        object.rotation.y -= 0.001;
        requestAnimationFrame(rotate);
      };
      rotate(); // 为模型添加自动旋转

      scene.add(object);

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
  addGrid();
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
