<script>
	import { Canvas } from '@threlte/core';
	import Scene from './Scene.svelte';
	import LiveState from '../../lib/livestate';

	let documentId = $state('dc377afa-d93a-418d-a27f-be0c79b306a5');
	let connected = $state(false);
	let doc = $state(null);
	let live = $state(null);
	let camx = $state(0);
	let camy = $state(0);
	let camz = $state(0);

	function connectToDocument(document_id) {
		if (live) {
			live.disconnect();
			connected = false;
		}

		live = new LiveState({
			url: 'ws://localhost:4000/redux',
			topic: `redux_document:${document_id}`
		});

		live.subscribe((serverState) => {
			doc = serverState.detail.state.document.data;
			connected = true;
		});

		live.connect();
	}
</script>

<svelte:head>
	<title>Renew Web Editor</title>
</svelte:head>

<h2>Renew Web Svelte Client + ThreeJS</h2>

{#if connected}
	<button
		style="position: absolute; top: 1em; left: 1em; z-index: 100"
		onclick={(evt) => {
			connected = false;
			live.disconnect();
		}}>disconnect</button
	>

	{#if doc}
		<div
			style="position: absolute; left:0; right:0; top:0; bottom:0; width: 100vw; height: 100vh"
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
			<Canvas>
				<Scene {doc} {camx} {camy} {camz} />
			</Canvas>
		</div>
	{/if}
{:else}
	<div style="display: flex; justify-content: center; align-items: end;">
		<label style="text-align: left;">
			Document Id:<br />
			<input bind:value={documentId} type="text" placeholder="UUID of a Document" />
		</label>
		<button
			onclick={(evt) => {
				connectToDocument(documentId);
			}}>connect</button
		>
	</div>
{/if}

<style>
	h2 {
		text-align: center;
	}

	input {
		padding: 1ex;
		min-width: 20em;
	}
	button {
		padding: 1ex;
	}
</style>
