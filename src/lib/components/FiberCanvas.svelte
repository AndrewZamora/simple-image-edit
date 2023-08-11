
<script>
	// @ts-nocheck
	import { fabric } from 'fabric';
	import { onMount } from 'svelte';
	/**
	 * @type {HTMLCanvasElement}
	 */
	let id;
	let canvas = null;
	let backgroundImage = null;
	export let height = 500;
	export let width = 500;

	function loadFromJSON(canvas, json) {
		return new Promise((resolve) => {
			canvas.loadFromJSON(json, () => {
				resolve();
			});
		});
	}

	function getBlob(canvas) {
		return new Promise((resolve) => {
			canvas.getElement().toBlob((blob) => resolve(blob));
		});
	}

	function addTextbox() {
		const textbox = new fabric.Textbox('this is a test', {
			top: 50,
			left: 10
		});
		canvas.add(textbox);
	}

	function getImage(url) {
		return new Promise((resolve) => {
			fabric.Image.fromURL(
				url,
				(img) => {
					resolve(img);
				},
				{ crossOrigin: 'anonymous' }
			);
		});
	}

	async function exportImage() {
		const json = canvas.toJSON();
		const exportScaleX = canvas.getWidth() / backgroundImage.width;
		const exportScaleY = canvas.getHeight() / backgroundImage.height;
		json.objects.forEach((object) => {
			if (object.type === 'textbox') {
				object.scaleX = object.scaleX / exportScaleX;
				object.scaleY = object.scaleY / exportScaleY;
				object.left = object.left / exportScaleX;
				object.top = object.top / exportScaleY;
			}
		});
		json.backgroundImage.scaleX = 1;
		json.backgroundImage.scaleY = 1;
		canvas.clear();
		canvas.renderAll();
		await loadFromJSON(canvas, json);
		canvas.setWidth(backgroundImage.width);
		canvas.setHeight(backgroundImage.height);
		canvas.renderAll();
		const blob = await getBlob(canvas);
		const blobUrl = await URL.createObjectURL(blob);
		console.log({blobUrl})
		return blobUrl;
	}

	async function setBackgroundImage(imgUrl) {
		backgroundImage = await getImage(imgUrl).catch((err) => console.log('err', err));
		console.log(backgroundImage);
		const widthAspectRatio = backgroundImage.height / backgroundImage.width;
		const canvasHeight = height ? height : width * widthAspectRatio;
		const canvasWidth = width ? width : height / widthAspectRatio;
		canvas.setWidth(canvasWidth);
		canvas.setHeight(canvasHeight);
		const imgDimensions = {
			scaleX: canvas.getWidth() / backgroundImage.width,
			scaleY: canvas.getHeight() / backgroundImage.height
		};
		canvas.setBackgroundImage(backgroundImage, canvas.renderAll.bind(canvas), imgDimensions);
	}

	onMount(async () => {
		canvas = new fabric.Canvas(id, { height, width });
		const rect = new fabric.Rect({
			top: 0,
			left: 100,
			width: 60,
			height: 70,
			fill: 'red'
		});
		canvas.add(rect);
		canvas.on('mouse:over', (event) => {
			console.log(event);
		});
		await setBackgroundImage('https://source.unsplash.com/random');
	});
</script>

<div class="index">
	<main class="container">
		<canvas class="canvas" bind:this={id} width="300" height="300" />
	</main>
	<button type="button" on:click={addTextbox}>Add Text</button>
	<button type="button" on:click={exportImage}>Export Image</button>
</div>

<style>
</style>
