<script lang="ts">
	import { onMount } from 'svelte';
	import LangSelector from './LangSelector.svelte';

	let navbar: HTMLElement;

	let scrollY = $state(0);
	let prevScrollpos = $derived(scrollY);

	let backgroundClasses = 'bg-opacity-0 border-opacity-0';

	onMount(() => {
		window.onscroll = async () => {
			autoHideNavbar();
		};
	});

	function autoHideNavbar() {
		let currentScrollPos = scrollY;
		if (prevScrollpos > currentScrollPos) {
			navbar.style.top = '0';
		} else {
			navbar.style.top = '-68px';
		}
		prevScrollpos = currentScrollPos;
	}
</script>

<svelte:window bind:scrollY />

<section
	bind:this={navbar}
	class="fixed top-0 right-0 left-0 z-40 flex items-center justify-end px-6 py-4 duration-300 lg:px-10 {backgroundClasses}"
>
	<LangSelector />
</section>
