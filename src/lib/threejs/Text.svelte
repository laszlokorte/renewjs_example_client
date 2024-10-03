<script>
	import { T } from '@threlte/core';
	import {
		MeshLineGeometry,
		MeshLineMaterial,
		Text3DGeometry,
		Text,
		useSuspense
	} from '@threlte/extras';
	import { TextGeometry } from 'three/examples/jsm/geometries/TextGeometry.js';
	import Text3DGeo from './Text3DGeo.svelte';
	let { body, x, y, color, z, size, ref = $bindable() } = $props();

	var textGeometry = new TextGeometry(body);
</script>

{#key body}
	<T.Mesh position={[x, y, z]} scale={[(size / 12) * 0.1, (size / 12) * 0.1, 0.0001]}>
		{#key body}
			<Text3DGeo text={body} />
		{/key}
		<T.MeshStandardMaterial {color} metalness={0} roughness={1} />
	</T.Mesh>
{/key}
