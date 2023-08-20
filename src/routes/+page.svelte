<script lang="ts">
	import FiberCanvas from '$lib/components/FiberCanvas.svelte';
	import { SvelteComponent, onMount } from 'svelte';
	let canvas: SvelteComponent;
	onMount(() => {
		if (canvas) {
			canvas.setBackgroundImage('https://source.unsplash.com/random');
		}
	});
</script>

<div class="index">
	<div class="container">
		<FiberCanvas bind:this={canvas} width={600} height={600} />
	</div>
</div>

<style>
	.container {
		display: flex;
		width: 100%;
		justify-content: center;
		align-items: center;
		outline: orangered dashed 1px;
	}
</style>
