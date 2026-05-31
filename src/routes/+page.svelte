<script>
	import { m } from '$lib/paraglide/messages';
	import videoSrc from '$lib/assets/depto-loop.webm';
	import NavBar from '$lib/components/NavBar.svelte';
</script>

<div
	class="flex h-screen w-full flex-col items-center justify-center overflow-x-hidden bg-[#E2E2DB] font-sans"
>
	<NavBar />

	<div class="absolute bottom-20 z-20 w-screen px-10 text-2xl lg:px-40 lg:text-end">
		{m.silly_stock_whale_lock()}
	</div>
	<video class="absolute w-2xl z-10 translate-x-5 max-w-none object-cover lg:w-full" src={videoSrc} autoplay muted loop></video>
	<!-- <Canvas>
			<Scene />‰
		</Canvas> -->
</div>
