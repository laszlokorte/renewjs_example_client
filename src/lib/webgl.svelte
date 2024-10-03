<script>
	import { Canvas } from '@threlte/core';

	import Scene from './threejs/Scene.svelte';

	const { doc, camx, camy, camz } = $props();
</script>

<div class="viewport">
	<Canvas size={{ width: 1000, height: 1000 }}>
		<Scene {doc} {camx} {camy} {camz} />
	</Canvas>
</div>

<style>
	.viewport {
		position: absolute;
		display: block;
		grid-row: 1 / span 2;
		width: 100%;
		height: 100%;
		max-width: 100%;
		max-height: 100%;
		align-self: stretch;
		justify-self: stretch;
	}
</style>
