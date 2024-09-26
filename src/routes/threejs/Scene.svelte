<script>
  import { T } from '@threlte/core'
  import { MeshLineGeometry, MeshLineMaterial, Text3DGeometry, Text } from '@threlte/extras'
  import { Vector3 } from 'three'

  let {doc, camx = 0, camy = 0, camz= 0} = $props()
</script>
<T.AmbientLight intensity={2}   />
<T.DirectionalLight intensity={2} position={[camx, camy, 10+camz]}  />
<T.PerspectiveCamera
  makeDefault
  position={[5+camx, 0-camy, camz]}
  on:create={({ ref }) => {
    ref.lookAt(0, 1, 0)
  }}
/>
{#each doc.elements.items as el, i (el.id)}
        {#if el.box && !el.hidden}
          <T.Mesh position={[(el.box.position_x+el.box.width/2)*0.01, -(el.box.position_y+el.box.height/2)*0.01, -10 + i/100]}>
<T.BoxGeometry args={[el.box.width*0.01, el.box.height*0.01, 0.05]} />
<T.MeshStandardMaterial color={el.style.background_color} />
</T.Mesh>

        {/if}
        {#if el.edge && !el.hidden}
          <T.Mesh >
<MeshLineGeometry
    points={[new Vector3(el.edge.source_x*0.01, -el.edge.source_y*0.01, -10), 
    ...el.edge.waypoints.map(w => new Vector3(w.x*0.01, -w.y*0.01, -10 + i/100)),
    new Vector3(el.edge.target_x*0.01, -el.edge.target_y*0.01, -10 + i/100)]}
  />
  <MeshLineMaterial
    width={2}
    color={"#000000"}
    attenuate={false}
  />
</T.Mesh>
        {/if}
        {#if el.text && !el.hidden}
        {/if}
        {/each}

        <!-- 
<T.Mesh position={[(el.text.position_x)*0.01, -(el.text.position_y)*0.01, -9 + i/500]} scale={[0.001,0.001, 0.0005]}>
          <Text3DGeometry
            text={el.text.body}
          />
          <T.MeshBasicMaterial 
            color={el.text.style.text_color}
            toneMapped={false}
            metalness={0}
            roughness={1}
          />
        </T.Mesh>
        -->