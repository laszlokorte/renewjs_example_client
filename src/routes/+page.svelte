<script>
	import LiveState from '../lib/livestate';
	import WebGL from '../lib/webgl.svelte';
	import Svg from '../lib/svg.svelte';

	let server = $state('renewcollab.laszlokorte.de/');
	let ssl = $state(true);
	let documentId = $state('a55e0dcc-a96e-4736-83bb-0a21ec2149ba');
	let connected = $state(false);
	let doc = $state(null);
	let live = $state(null);
	let renderer = $state('svg');

	let camx = $state(0);
	let camy = $state(0);
	let camz = $state(-1);

	function connectToDocument(ssl, server_url, document_id) {
		if (live) {
			live.disconnect();
			connected = false;
		}

		live = new LiveState({
			url: `ws${ssl ? 's' : ''}://${server.replace(/^(http|ws)s?:\/\//, '')}/redux`,
			topic: `redux_document:${document_id}`,
			socketOptions: { params: { token: 'DEBUG' } }
		});

		live.subscribe((serverState) => {
			doc = serverState.detail.state;
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
			<img alt="Logo" src={'./favicon.svg'} width="28" height="28" align="top" /> Renew Web Svelte Client
			(SVG)
		</h2>

		<div style="text-align: center; display: flex; justify-content: center; justify-items: center;">
			Renderer:
			<label><input type="radio" bind:group={renderer} value="svg" /> SVG</label>
			<label><input type="radio" bind:group={renderer} value="webgl" /> WebGL</label>
		</div>
	</header>

	{#if connected}
		{#if doc}
			<div
				class="interactive"
				onpointerdown={(evt) => {
					evt.currentTarget.setPointerCapture(evt.pointerId);
				}}
				onpointermove={(evt) => {
					if (evt.buttons) {
						camx -= (evt.movementX / 60) * Math.exp(camz / 100);
						camy -= (evt.movementY / 60) * Math.exp(camz / 100);
					}
				}}
				onwheel={(evt) => {
					camz += evt.deltaY / 100;
				}}
			>
				{#if renderer == 'svg'}
					<Svg {doc} {camx} {camy} {camz} />
				{/if}
				{#if renderer == 'webgl'}
					<WebGL {doc} {camx} {camy} {camz} />
				{/if}
			</div>
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
				<div style="display: block; text-align: left;">
					<label for="server">Server:</label> (<label
						><input type="checkbox" bind:checked={ssl} /> SSL</label
					>)<br />
					<input
						id="server"
						bind:value={server}
						type="text"
						placeholder="URL to Renew Web Editor Server"
					/>
				</div>
				<label style="display: block; text-align: left;">
					Document Id:<br />
					<input bind:value={documentId} type="text" placeholder="UUID of a Document" />
				</label>
			</div>
			<button
				onclick={(evt) => {
					connectToDocument(ssl, server, documentId);
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

	.interactive {
		display: contents;
	}
</style>
