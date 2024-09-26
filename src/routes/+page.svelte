<script>
	import LiveState from '../lib/livestate';

	let documentId = $state('');
	let connected = $state(false);
	let doc = $state(null);
	let live = $state(null);

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

<h2>Renew Web Svelte Client</h2>

{#if connected}
	<button
		style="position: absolute; top: 1em; left: 1em"
		onclick={(evt) => {
			connected = false;
			live.disconnect();
		}}>disconnect</button
	>

	{#if doc}
		<svg
			viewBox="0 0 1000 1000"
			style="position: absolute; top: 3em; left:0;right:0;bottom: 0; width: 100vw; height: 90vh"
		>
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
