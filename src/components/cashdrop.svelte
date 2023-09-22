<script lang="ts">
  import { onMount } from "svelte";
  import * as THREE from "three";

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
    const renderer = new THREE.WebGLRenderer({ canvas: canvas });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(window.devicePixelRatio);
    const geometry = new THREE.BoxGeometry(1, 1, 1);
    const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);
    camera.position.z = 5;

    function animate() {
      requestAnimationFrame(animate);
      cube.rotation.x += 0.01;
      cube.rotation.y += 0.01;
      renderer.render(scene, camera);
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
