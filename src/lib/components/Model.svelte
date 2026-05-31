<!-- src/lib/components/Model.svelte -->
<script>
	import { T } from '@threlte/core';
	import { useGltf, useDraco } from '@threlte/extras';

	const gltf = useGltf('/depto-crudo.glb', { dracoLoader: useDraco() });
</script>

<T.Group dispose={false}>
	{#await gltf}
		<T.Mesh>
			<T.BoxGeometry />
			<T.MeshStandardMaterial color="gray" wireframe />
		</T.Mesh>
	{:then data}
		<T.Mesh geometry={data?.nodes.mesh_0.geometry} material={data?.nodes.mesh_0.material} />
		<T.Mesh geometry={data?.nodes.mesh_1.geometry} material={data?.nodes.mesh_1.material} />
	{:catch err}
		<T.Mesh>
			<T.BoxGeometry />
			<T.MeshStandardMaterial color="red" />{err.message}
		</T.Mesh>
	{/await}
</T.Group>
