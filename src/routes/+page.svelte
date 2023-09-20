<script lang="ts">
	import FabricCanvas from '$lib/components/FabricCanvas.svelte';
	import { SvelteComponent, onMount } from 'svelte';
	let canvas: SvelteComponent;
	let allowZoom = false;
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
	function addImage(image: HTMLImageElement) {
		canvas.addImage(image);
	}
	function addTextbox(text: string, { top, left }: { top: number; left: number }) {
		canvas.addTextbox(text, { top, left });
	}
	function handleImageFiles(event: Event) {
		const files = (<HTMLInputElement>event.target).files;
		if (files?.length) {
			Array.from(files).forEach((file) => {
				uploadImageFiles(file);
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
	function handleCrop() {
		canvas.crop();
		allowZoom = false;
	}
	function resetCanvas() {
		canvas.reset();
	}
</script>

<div class="index">
	<div class="container">
		<FabricCanvas bind:this={canvas} width={600} height={600} backgroundColor="gray" {allowZoom} />
		<button on:click={exportCanvas}>Export</button>
		<button type="button" on:click={() => addTextbox('click to edit', { top: 50, left: 50 })}
			>Add Text</button
		>
		<button on:click={() => (allowZoom = true)} type="button">zoom</button>
		<button on:click={handleCrop} type="button">crop</button>
		<button on:click={resetCanvas} type="button">reset</button>
		<input
			type="file"
			on:change={(event) => handleImageFiles(event)}
			name="add-image"
			id="add-image"
			multiple
		/>
	</div>
	{#if images.length}
		<div class="image-gallery">
			{#each images as image}
				<button type="button" on:click={() => addImage(image)}>
					<img src={image.currentSrc} alt="file preview" />
				</button>
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
	.image-gallery img {
		height: auto;
		width: 300px;
	}
</style>
