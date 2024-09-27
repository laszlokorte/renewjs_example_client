<script>
	import { Canvas } from '@threlte/core';
	import Scene from './Scene.svelte';
	import LiveState from '../../lib/livestate';

	let server = $state('localhost:4000');
	let documentId = $state('dc377afa-d93a-418d-a27f-be0c79b306a5');
	let connected = $state(false);
	let doc = $state(null);
	let live = $state(null);
	let camx = $state(0);
	let camy = $state(0);
	let camz = $state(0);

	function connectToDocument(server, document_id) {
		if (live) {
			live.disconnect();
			connected = false;
		}

		live = new LiveState({
			url: `ws://${server}/redux`,
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

<article>
	<header>
		<h2>
			<img src="/favicon.svg" width="28" height="28" align="top" /> Renew Web Svelte Client (WebGL)
		</h2>

		<p style="text-align: center;">
			<a href="/">SVG Renderer</a>
		</p>
	</header>

	{#if connected}
		{#if doc}
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

			<button
				style="position: absolute; top: 1em; left: 1em; z-index: 100"
				onclick={(evt) => {
					connected = false;
					live.disconnect();
				}}>disconnect</button
			>
		{/if}
	{:else}
		<div style="display: flex; justify-content: center; align-items: end;">
			<div>
				<label style="display: block; text-align: left;">
					Server:<br />
					<input bind:value={server} type="text" placeholder="URL to Renew Web Editor Server" />
				</label>
				<label style="display: block; text-align: left;">
					Document Id:<br />
					<input bind:value={documentId} type="text" placeholder="UUID of a Document" />
				</label>
			</div>
			<button
				onclick={(evt) => {
					connectToDocument(server, documentId);
				}}>connect</button
			>
		</div>
	{/if}
</article>

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

	article {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		display: grid;
		grid-template-rows: auto 1fr;
		align-items: start;
		width: 100vw;
		height: 100vh;
	}

	header {
		z-index: 100;
		background: #fffa;
	}

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
