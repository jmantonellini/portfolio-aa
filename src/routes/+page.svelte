<script>
	import { m } from '$lib/paraglide/messages';
	import videoSrc from '$lib/assets/depto-loop.webm';
	import NavBar from '$lib/components/NavBar.svelte';
</script>

<div class="flex h-screen w-full flex-col items-center justify-center bg-[#E2E2DB] font-sans">
	<NavBar />

	<div class="absolute left-10 lg:right-40 lg:left- bottom-20 text-2xl">
		{m.silly_stock_whale_lock()}
	</div>
	<video class="h-full w-full" src={videoSrc} autoplay muted loop></video>
	<!-- <Canvas>
			<Scene />‰
		</Canvas> -->
</div>
