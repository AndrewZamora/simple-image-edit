<script lang="ts">
	import FiberCanvas from '$lib/components/FiberCanvas.svelte';
	import { SvelteComponent, onMount } from 'svelte';
	let canvas: SvelteComponent;
	onMount(() => {
		if (canvas) {
			canvas.setBackgroundImage('https://source.unsplash.com/random');
		}
	});
	async function exportCanvas(){
		const blobUrl = await canvas.exportImage();
		download(blobUrl)
	}
	function download(blobUrl: string) {
		const anchor = document.createElement('a');
		anchor.download = 'test.jpeg';
		anchor.href = blobUrl;
		anchor.click();
	}
	function addImage(event: Event) {
		canvas.addImage(event);
	}
</script>

<div class="index">
	<div class="container">
		<FiberCanvas bind:this={canvas} width={600} height={600} />
		<button on:click={exportCanvas}>Export</button>
		<input type="file" on:change={(event)=>addImage(event)} name="add-image" id="add-image">
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
