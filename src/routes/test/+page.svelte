<script lang="ts">
	import FabricCanvas from '$lib/components/FabricCanvas.svelte';
	import { SvelteComponent, onMount } from 'svelte';
	let canvas: SvelteComponent;
	let savedImages: HTMLImageElement[] = [];
	let allowZoom = false;
	let showCanvas = false;
	let showFileInput = true;
	let activeObject = null;
	$: images = savedImages;

	function addImage(image: HTMLImageElement) {
		canvas.addImage(image);
	}
	function handleImageFiles(event: Event) {
		const files = (<HTMLInputElement>event.target).files;
		if (files?.length) {
			Array.from(files).forEach((file) => {
				uploadImageFile(file);
			});
		}
		showFileInput = false;
	}
	function uploadImageFile(file: File) {
		showCanvas = true;
		const reader = new FileReader();
		reader.onload = (event: Event) => {
			let image = new Image();
			const result = (<FileReader>event.target).result;
			if (result) {
				image.src = result.toString();
				image.onload = () => {
					savedImages.push(image);
					addImage(image);
					savedImages = savedImages;
				};
			}
		};
		reader.readAsDataURL(file);
	}
	function handleCrop() {
		console.log({activeObject})
	}
	function onSelect({detail}) {
		activeObject = detail;
	}
	onMount(() => {});
</script>

<main class="container">
	{#if showFileInput}
		<input
			type="file"
			on:change={(event) => handleImageFiles(event)}
			name="add-image"
			id="add-image"
			multiple
		/>
	{/if}
	{#if showCanvas}
		<FabricCanvas bind:this={canvas} width={600} height={600} backgroundColor="gray" {allowZoom} on:select={onSelect} />
		<button on:click={handleCrop} type="button">crop</button>
	{/if}
</main>

<style>
	.container {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100vh;
	}
	.hidden {
		display: hidden;
		background: green;
	}
</style>
