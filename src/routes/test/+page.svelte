<script lang="ts">
	import FabricCanvas from '$lib/components/FabricCanvas.svelte';
	import { SvelteComponent, onMount } from 'svelte';
	let canvas: SvelteComponent;
	let cropCanvas: SvelteComponent;
	let savedImages: HTMLImageElement[] = [];
	let allowZoom = false;
	let showCanvas = false;
	let showCrop = false;
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
				image.id = `${file.name}${new Date().getTime()}`;
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
		const imageMatch = savedImages.find((img) => img.id === activeObject['_element'].id);
		showCanvas = false;
		showCrop = true;
		setTimeout(() => {
			if (imageMatch && imageMatch.src) {
				cropCanvas.setBackgroundImage(imageMatch.src);
			}
		}, 0);
	}
	function onSelect({ detail }) {
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
		<FabricCanvas
			bind:this={canvas}
			width={600}
			height={600}
			backgroundColor="gray"
			{allowZoom}
			on:select={onSelect}
		/>
		<button on:click={handleCrop} type="button">crop</button>
	{/if}
	{#if showCrop}
		<FabricCanvas
			bind:this={cropCanvas}
			width={600}
			height={600}
			backgroundColor="gray"
			allowZoom={true}
		/>
	{/if}
</main>

<style>
	.container {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 100vh;
	}
</style>
