<script>
	// @ts-nocheck
	import { fabric } from 'fabric';
	import { onMount } from 'svelte';
	/**
	 * @type {HTMLCanvasElement}
	 */
	let id;
	onMount(() => {
		const canvas = new fabric.Canvas(id, { height: 500, width: 500 });
		const rect = new fabric.Rect({
			top: 0,
			left: 100,
			width: 60,
			height: 70,
			fill: 'red'
		});
		const textbox = new fabric.Textbox("this is a test", {
			top: 50,
			left: 10,
		})
		canvas.add(textbox)
		canvas.add(rect);
		canvas.on('mouse:over', (event) => {
			console.log(event);
		});
	});

	function setBackgroundImage() {}
</script>

<div class="index">
	<main class="container">
		<canvas class="canvas" bind:this={id} width="300" height="300" />
	</main>
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
