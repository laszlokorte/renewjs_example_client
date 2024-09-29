<script>
	import { T } from '@threlte/core';
	import { MeshLineGeometry, MeshLineMaterial, Text3DGeometry } from '@threlte/extras';
	import { Vector3 } from 'three';
	import Text from './Text.svelte';

	let { doc, camx = 0, camy = 0, camz = 0 } = $props();
</script>

<T.AmbientLight intensity={2} />
<T.DirectionalLight intensity={2} position={[camx, camy, 10 + camz]} />
<T.PerspectiveCamera
	makeDefault
	position={[5 + camx, 0 - camy, camz]}
	on:create={({ ref }) => {
		ref.lookAt(0, 1, 0);
	}}
/>
{#each doc.elements.items as el, i (el.id)}
	{#if el.box && !el.hidden}
		<T.Mesh
			position={[
				(el.box.position_x + el.box.width / 2) * 0.01,
				-(el.box.position_y + el.box.height / 2) * 0.01,
				-10 + i / 100
			]}
		>
			<T.BoxGeometry args={[el.box.width * 0.01, el.box.height * 0.01, 0.05]} />
			<T.MeshStandardMaterial color={el.style.background_color} />
		</T.Mesh>
	{/if}
	{#if el.edge && !el.hidden}
		<T.Mesh>
			{#key el.edge.waypoints.length}
				<MeshLineGeometry
					points={[
						new Vector3(el.edge.source_x * 0.01, -el.edge.source_y * 0.01, -10 + i / 100),
						...el.edge.waypoints.map((w) => new Vector3(w.x * 0.01, -w.y * 0.01, -10 + i / 100)),
						new Vector3(el.edge.target_x * 0.01, -el.edge.target_y * 0.01, -10 + i / 100)
					]}
				/>
			{/key}
			<MeshLineMaterial width={0.02} color={'#000000'} attenuate={true} />
		</T.Mesh>
	{/if}
	{#if el.text && !el.hidden}
		<Text
			{i}
			body={el.text.body}
			x={el.text.position_x}
			y={el.text.position_y}
			color={el.text.style.text_color || 'black'}
		/>
	{/if}
{/each}

<!-- 

        -->
