<script>
	import FiberCanvas from '$lib/components/FiberCanvas.svelte';
</script>

<div class="index">
	<div class="container">
		<FiberCanvas />
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
	.canvas {
		outline: green dashed 1px;
	}
</style>
