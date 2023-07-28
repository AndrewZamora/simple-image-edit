<script>
	// @ts-nocheck
	import { fabric } from 'fabric';
	import { onMount } from 'svelte';
	/**
	 * @type {HTMLCanvasElement}
	 */
	let id;
	let canvas = null;
	// let backgroundImage = null;
	export let height = 500;
	export let width = 500;

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

	async function setBackgroundImage(imgUrl) {
		let backgroundImage = await getImage(imgUrl).catch((err) => console.log('err', err));
		console.log(backgroundImage)
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
</div>

<style>
	.container {
		display: flex;
		width: 100%;
		justify-content: center;
		align-items: center;
		outline: orangered dashed 1px;
	}
	.canvas {
		outline: green dashed 1px;
	}
</style>
