<!-- src/routes/Scene.svelte -->
<script lang="ts">
  import { T } from '@threlte/core';
  import Model from '$lib/components/Model.svelte';
  import { OrbitControls } from '@threlte/extras';
</script>

<T.PerspectiveCamera
  makeDefault
  position={[5, 4, 10]}
  fov={50}
/>

<T.DirectionalLight position={[5, 5, 5]} intensity={1.5} />
<T.AmbientLight intensity={1} />

<OrbitControls enableDamping />

<Model />