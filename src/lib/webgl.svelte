<script>
	import { Canvas } from '@threlte/core';

	import Scene from './threejs/Scene.svelte';

	const { doc } = $props();
	let camx = $state(0);
	let camy = $state(0);
	let camz = $state(0);
</script>

<div
	class="viewport"
	onpointermove={(evt) => {
		if (evt.buttons) {
			camx -= (evt.movementX / 100) * Math.exp(-camz / 100);
			camy -= (evt.movementY / 100) * Math.exp(-camz / 100);
		}
	}}
	onwheel={(evt) => {
		camz += evt.deltaY / 100;
	}}
>
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
