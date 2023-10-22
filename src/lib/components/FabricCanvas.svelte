<script lang="ts">
	// @ts-nocheck
	import { fabric } from 'fabric';
	import { onMount, createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();
	export let height = 500;
	export let width = 500;
	export let backgroundColor = '';
	export let allowZoom = false;
	let id;
	let canvas = null;
	let backgroundImage = null;

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

	export function addTextbox(text: string, { top, left }: { top: number; left: number }) {
		const textbox = new fabric.Textbox(text, {
			top,
			left
		});
		canvas.add(textbox);
	}
	export async function crop() {
		const dataUrl = canvas.toDataURL();
		await setBackgroundImage(dataUrl);
		// https://stackoverflow.com/questions/28863678/fabricjs-canvas-reset-after-zooming
		canvas.setZoom(1);
	}

	export function addImage(image: HTMLImageElement) {
		console.log('ADD IMAGE');
		let canvasImage = new fabric.Image(image);
		canvasImage.set({
			top: 0,
			left: 10
		});
		canvasImage.scaleToWidth(300);
		canvas.add(canvasImage).setActiveObject(canvasImage).renderAll();
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

	export async function getCropped() {
		return await getBlob(canvas);
	}

	export async function exportImage() {
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
		return blobUrl;
	}

	export async function setBackgroundImage(imgUrl) {
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

	export function clearBackgroundImage() {
		canvas.setBackgroundImage('', canvas.renderAll.bind(canvas));
		canvas.setZoom(1);
		// https://stackoverflow.com/questions/28863678/fabricjs-canvas-reset-after-zooming
		canvas.setViewportTransform([1,0,0,1,0,0])
	}

	export function reset() {
		canvas.dispose();
	}

	export function remove(activeObject) {
		canvas.remove(activeObject);
	}

	export function add(activeObject) {
		// canvas.add(activeObject)
		canvas.setBackgroundImage(activeObject, canvas.renderAll.bind(canvas))
	}

	function handleMouseWheel(opt) {
		if (allowZoom) {
			let delta = opt.e.deltaY;
			let zoom = canvas.getZoom();
			zoom *= 0.999 ** delta;
			if (zoom > 20) {
				zoom = 20;
			}
			if (zoom < 0.01) {
				zoom = 0.01;
			}
			canvas.zoomToPoint({ x: opt.e.offsetX, y: opt.e.offsetY }, zoom);
			canvas.setZoom(zoom);
			opt.e.preventDefault();
			opt.e.stopPropagation();
		}
	}
	function handleSelection() {
		dispatch('select', canvas.getActiveObject());
	}

	onMount(async () => {
		canvas = new fabric.Canvas(id, { height, width, backgroundColor });
		// canvas.on('mouse:over', (event) => {
		// 	console.log(event);
		// })
		canvas.on('mouse:wheel', (opt) => handleMouseWheel(opt));
		canvas.on('selection:updated', (obj) => handleSelection());
		canvas.on('selection:created', (obj) => handleSelection());
	});
</script>

<div class="index">
	<main class="container">
		<canvas class="canvas" bind:this={id} {width} {height} />
	</main>
</div>

<style>
</style>
