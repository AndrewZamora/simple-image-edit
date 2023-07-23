<script>
	// @ts-nocheck
	import { fabric } from 'fabric';
	import { onMount } from 'svelte';
	/**
	 * @type {HTMLCanvasElement}
	 */
	let id;

	onMount(() => {
		const canvas = new fabric.Canvas(id);
		const rect = new fabric.Rect({
			top: 100,
			left: 100,
			width: 60,
			height: 70,
			fill: 'red'
		});
		canvas.add(rect);
	});
</script>

<div class="index">
	<canvas bind:this={id} width="300" height="300" />
</div>
