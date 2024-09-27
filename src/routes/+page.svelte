<script>
	import LiveState from '../lib/livestate';

	let server = $state('localhost:4000');
	let documentId = $state('dc377afa-d93a-418d-a27f-be0c79b306a5');
	let connected = $state(false);
	let doc = $state(null);
	let live = $state(null);

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
			<img src="/favicon.svg" width="28" height="28" align="top" /> Renew Web Svelte Client (SVG)
		</h2>

		<p style="text-align: center;">
			<a href="/threejs">WebGL Renderer</a>
		</p>
	</header>

	{#if connected}
		{#if doc}
			<svg viewBox="0 0 1000 1000">
				{#each doc.elements.items as el}
					{#if el.box && !el.hidden}
						<rect
							fill={el.style.background_color ?? 'pink'}
							x={el.box.position_x}
							y={el.box.position_y}
							width={el.box.width}
							height={el.box.height}
						></rect>
					{/if}
					{#if el.text && !el.hidden}
						<text
							fill={el.text.style.text_color ?? 'pink'}
							x={el.text.position_x}
							y={el.text.position_y}
						>
							{#each el.text.body.split('\n') as line, li}
								<tspan x={el.text.position_x} dy={14}>{line}</tspan>
							{/each}
						</text>
					{/if}
					{#if el.edge && !el.hidden}
						<polyline
							points="{el.edge.source_x} {el.edge.source_y} {el.edge.waypoints
								.map((w) => `${w.x} ${w.y}`)
								.join(' ')} {el.edge.target_x} {el.edge.target_y}"
							stroke="black"
							fill="none"
							stroke-width="2"
						/>
					{/if}
				{/each}
			</svg>
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

	input {
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

	svg {
		display: block;
		width: 100%;
		max-height: 100%;
	}
</style>
