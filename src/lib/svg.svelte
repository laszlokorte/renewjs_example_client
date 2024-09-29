<script>
	const { doc } = $props();
</script>

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
			<text fill={el.text.style.text_color ?? 'pink'} x={el.text.position_x} y={el.text.position_y}>
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

<style>
	svg {
		display: block;
		width: 100%;
		max-height: 100%;
	}
</style>
