<script>
	import { m } from '$lib/paraglide/messages';
	import videoSrc from '$lib/assets/depto-loop.webm';
	import NavBar from '$lib/components/NavBar.svelte';
	import { onMount } from 'svelte';

	let videoElement = $state();
	let videoUrl = $state('');

	onMount(() => {
		// Asegura que el video se cargue después de la hidratación
		videoUrl = videoSrc;

		// Forzar reproducción si falla autoplay
		if (videoElement) {
			videoElement.play().catch((e) => console.log('Autoplay prevented:', e));
		}
	});
</script>

<div
	class="flex h-screen w-full flex-col items-center justify-center overflow-x-hidden bg-[#E2E2DB] font-sans"
>
	<NavBar />

	<div class="absolute bottom-20 z-20 w-screen px-10 text-2xl lg:px-40 lg:text-end">
		{m.silly_stock_whale_lock()}
	</div>
	{#if videoUrl}
		<video
			bind:this={videoElement}
			class="absolute z-10 w-2xl max-w-none translate-x-5 object-cover lg:w-full"
			src={videoUrl}
			autoplay
			muted
			loop
		></video>
	{/if}
	<!-- <Canvas>
			<Scene />‰
		</Canvas> -->
</div>
