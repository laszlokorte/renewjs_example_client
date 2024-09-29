<script>
	import LiveState from '../lib/livestate';
	import WebGL from '../lib/webgl.svelte';
	import Svg from '../lib/svg.svelte';

	let server = $state('localhost:4000');
	let documentId = $state('b67111c3-a727-4cc9-b14d-702ab9534d09');
	let connected = $state(false);
	let doc = $state(null);
	let live = $state(null);
	let renderer = $state('svg');

	function connectToDocument(server_url, document_id) {
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
			<img src={'./favicon.svg'} width="28" height="28" align="top" /> Renew Web Svelte Client (SVG)
		</h2>

		<div style="text-align: center; display: flex; justify-content: center; justify-items: center;">
			Renderer:
			<label><input type="radio" bind:group={renderer} value="svg" /> SVG</label>
			<label><input type="radio" bind:group={renderer} value="webgl" /> WebGL</label>
		</div>
	</header>

	{#if connected}
		{#if doc}
			{#if renderer == 'svg'}
				<Svg {doc} />
			{/if}
			{#if renderer == 'webgl'}
				<WebGL {doc} />
			{/if}
		{/if}
		<button
			style="position: absolute; top: 1em; left: 1em; z-index: 1000"
			onclick={(evt) => {
				connected = false;
				live.disconnect();
			}}>disconnect</button
		>
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

	input[type='text'] {
		padding: 1ex;
		min-width: 20em;
	}
	button {
		padding: 1ex;
	}

	article {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		display: grid;
		grid-template-rows: auto 1fr;
		align-items: start;
		width: 100%;
		height: 100%;
	}

	header {
		z-index: 100;
		background: #fffa;
	}
</style>
