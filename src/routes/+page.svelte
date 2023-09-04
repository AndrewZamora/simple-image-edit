<script lang="ts">
	import FiberCanvas from '$lib/components/FiberCanvas.svelte';
	import { SvelteComponent, onMount } from 'svelte';
	let canvas: SvelteComponent;
	onMount(() => {
		if (canvas) {
			canvas.setBackgroundImage('https://source.unsplash.com/random');
		}
	});
	let savedImages: HTMLImageElement[] = [];
	$: images = savedImages;
	async function exportCanvas() {
		const blobUrl = await canvas.exportImage();
		download(blobUrl);
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
	function handleImageFiles(event: Event) {
		const files = (<HTMLInputElement>event.target).files;
		if (files?.length) {
			Array.from(files).forEach((file) => {
				uploadImageFiles(file)
			});
		}
	}
	function uploadImageFiles(file: File) {
		const reader = new FileReader();
		reader.onload = (event: Event) => {
			let image = new Image();
			const result = (<FileReader>event.target).result;
			if (result) {
				image.src = result.toString();
				image.onload = () => {
					savedImages.push(image);
					savedImages = savedImages;
				};
			}
		};
		reader.readAsDataURL(file);
	}
</script>

<div class="index">
	<div class="container">
		<FiberCanvas bind:this={canvas} width={600} height={600} />
		<button on:click={exportCanvas}>Export</button>
		<input
			type="file"
			on:change={(event) => handleImageFiles(event)}
			name="add-image"
			id="add-image"
			multiple
		/>
	</div>
	{#if images.length}
	<div>
		{#each images as image }
			<img src={image.currentSrc} alt="file previews">
		{/each}
	</div>
	{/if}
</div>

<style>
	.container {
		display: flex;
		width: 100%;
		justify-content: center;
		align-items: center;
		outline: orangered dashed 1px;
	}
	img {
		height: 100%;
		width: auto;
	}
</style>
