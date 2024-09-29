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
	let { body, x, y, color, i, ref = $bindable(), children } = $props();

	var textGeometry = new TextGeometry(body);
</script>

{#key body}
	<T.Mesh position={[x * 0.01, -y * 0.01, -9 + i / 500]} scale={[0.001, 0.001, 0.0005]}>
		{#key body}
			<Text3DGeo text={body} />
		{/key}
		<T.MeshBasicMaterial {color} toneMapped={false} metalness={0} roughness={1} />
	</T.Mesh>
{/key}
