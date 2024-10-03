<script>
	import { T } from '@threlte/core';
	import { MeshLineGeometry, MeshLineMaterial, Text3DGeometry } from '@threlte/extras';
	import { Vector3 } from 'three';
	import Text from './Text.svelte';

	let { doc, camx, camy, camz } = $props();
</script>

<T.AmbientLight intensity={4} />
<T.DirectionalLight intensity={4} position={[camx, camy, -10 + Math.exp(-camz / 20)]} />
<T.OrthographicCamera
	makeDefault
	position={[100 * camx, -100 * camy, 1]}
	left={-700}
	top={700}
	right={+700}
	bottom={-700}
	zoom={Math.exp(-camz / 20) / 2}
	on:create={({ ref }) => {
		ref.updateProjectionMatrix();
	}}
/>
{#each doc.elements.items as el, i (el.id)}
	{#if el.box && !el.hidden}
		<T.Mesh
			position={[
				el.box.position_x + el.box.width / 2,
				-el.box.position_y - el.box.height / 2,
				-5 + el.z_index / 100
			]}
		>
			<T.BoxGeometry args={[el.box.width, el.box.height, 0.05]} />
			<T.MeshStandardMaterial color={el?.style?.background_color} />
		</T.Mesh>
	{/if}
	{#if el.edge && !el.hidden}
		{@const source_z = el.edge.source_bond
			? doc.elements.items.find((l) => l.id == el.edge.source_bond.layer_id).z_index
			: el.z_index}
		{@const target_z = el.edge.target_bond
			? doc.elements.items.find((l) => l.id == el.edge.target_bond.layer_id).z_index
			: el.z_index}
		<T.Mesh>
			{#key el.edge.waypoints.length}
				<MeshLineGeometry
					points={[
						new Vector3(el.edge.source_x, -el.edge.source_y, -5 + source_z / 100),
						...el.edge.waypoints.map((w) => new Vector3(w.x, -w.y, -5 + el.z_index / 100)),
						new Vector3(el.edge.target_x, -el.edge.target_y, -5 + target_z / 100)
					]}
				/>
			{/key}
			<MeshLineMaterial width={0.003} color={'#000000'} attenuate={true} />
		</T.Mesh>
	{/if}
	{#if el.text && !el.hidden}
		<T.Mesh
			position={[
				el.text.position_x + el.text.width / 2,
				-el.text.position_y - el.text.height / 2,
				-10 - el.z_index / 100
			]}
		>
			<T.BoxGeometry args={[3, 3, 0.05]} />
			<T.MeshStandardMaterial color={el?.text?.style?.text_color || 'black'} />
		</T.Mesh>
		<Text
			body={el.text.body}
			x={el.text.position_x}
			y={-el.text.position_y}
			z={-5 + el.z_index / 100}
			size={el?.text?.style?.font_size || 12}
			color={el?.text?.style?.text_color || 'black'}
		/>
	{/if}
{/each}

<!-- 

        -->
