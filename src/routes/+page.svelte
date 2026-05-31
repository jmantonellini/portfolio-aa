<script>
	import { Canvas } from '@threlte/core';
	import Scene from './Scene.svelte';
	import bg from '$lib/assets/hero-bg.svg';
	import NavBar from '$lib/components/NavBar.svelte';
</script>

<div class="h-screen w-full">
	<NavBar />
	<div
		class="scroll-bg-animation pointer-events-none absolute inset-0 -z-10 overflow-hidden bg-[0_-100px] bg-repeat will-change-transform"
		style="background-image: url({bg});"
	></div>
	<Canvas>
		<Scene />
	</Canvas>
</div>

<style>
	.dark:root {
		--cursor-color: #fff;
	}

	.scroll-bg-animation {
		--bg-height: clamp(800px, 100vh, 1350px);
		width: calc(100% + (var(--bg-height) * 16 / 9));
		background-size: calc(var(--bg-height) * 16 / 9) var(--bg-height);
		animation: scroll-horizontal 67s linear infinite;
	}

	@media (min-width: 768px) {
		.scroll-bg-animation {
			--bg-height: clamp(600px, 100vh, 1350px);
		}
	}

	@keyframes scroll-horizontal {
		from {
			transform: translate3d(0, 0, 0);
		}
		to {
			transform: translate3d(calc(var(--bg-height) * -16 / 9), 0, 0);
		}
	}
</style>
