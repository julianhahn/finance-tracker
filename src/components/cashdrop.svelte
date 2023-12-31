<script lang="ts">
  import { onMount } from "svelte";
  import * as THREE from "three";
  import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
  import { OrbitControls } from "three/addons/controls/OrbitControls.js";

  let canvas: HTMLCanvasElement | null = null;

  onMount(() => {
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );

    if (!canvas) throw new Error("Canvas not found");
    const renderer = new THREE.WebGLRenderer({
      canvas: canvas,
      antialias: true,
    });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(window.devicePixelRatio);
    camera.position.z = 5;
    const controls = new OrbitControls(camera, renderer.domElement);
    controls.update();

    const loader = new GLTFLoader();
    let model;
    let mixer: THREE.AnimationMixer | null = null;

    loader.load("dollar_bill_swing.glb", (gltf: any) => {
      model = gltf.scene;
      model.scale.set(1, 1, 1); // Scale if necessary
      let light = new THREE.AmbientLight(0xffffff, 1);
      scene.add(light);
      scene.add(model);

      // If you have animations:
      mixer = new THREE.AnimationMixer(model);

      gltf.animations.forEach((clip: any) => {
        if (!mixer) {
          throw new Error("Mixer not found");
        }
        mixer.clipAction(clip).repetitions = Infinity;
        mixer.clipAction(clip).play();
      });
    });

    function animate() {
      requestAnimationFrame(animate);
      mixer?.update(0.01);
      renderer.render(scene, camera);
      controls.update();
    }
    animate();
  });
</script>

<div class="cashdrop">
  <canvas bind:this={canvas} />
</div>

<style lang="scss">
  .cashdrop,
  canvas {
    width: 100%;
    height: 100%;
  }
</style>
