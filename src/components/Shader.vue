<template>
	<div class="shader"></div>
</template>

<script>

	import * as THREE from '@/assets/vendors/three.js/build/three.module.js';
	import * as graphics from '@/assets/js/graphics.js'; const gfx = graphics.default;
	
	var renderer, scene, camera, controls, clock, time = 0, canvas;
	let uniforms, material;
	
	export default {
		
		name: 'Shader',
		
		props: {
			msg: String
		},
		
		mounted: function() {
			
			scene = gfx.setUpScene();
			renderer = gfx.setUpRenderer(renderer);
			canvas = renderer.domElement;
			camera = gfx.setUpCamera(camera);
			scene.background = new THREE.Color('#000');
			controls = gfx.enableControls(controls, renderer, camera);
			gfx.resizeRendererOnWindowResize(renderer, camera);
			gfx.setCameraLocation(camera, new THREE.Vector3(10, 10, 10));
			clock = new THREE.Clock(); clock.start();
			
			this.shader();
			
			var animate = () => {
				requestAnimationFrame(animate);
				controls.update();
				this.eachFrame();
				renderer.render(scene, camera);
			};
			animate(); 
		},
		
		methods: {
			
			eachFrame: function() {
				time += clock.getDelta();
				uniforms.iResolution.value.set(canvas.width, canvas.height, 1);
				uniforms.iTime.value = time;
			},
			
			shader: function() {
				
				if (renderer) renderer.autoClearColor = false;
				const plane = new THREE.PlaneGeometry(2, 2);
				const fragmentShader = 
				`
					#include <common>

					uniform vec3 iResolution;
					uniform float iTime;

					// By iq: https://www.shadertoy.com/user/iq  
					// license: Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License.
					void mainImage( out vec4 fragColor, in vec2 fragCoord )
					{
						// Normalized pixel coordinates (from 0 to 1)
						vec2 uv = fragCoord/iResolution.xy;

						// Time varying pixel color
						vec3 col = 0.5 + 0.5*cos(iTime+uv.xyx+vec3(0,2,4));

						// Output to screen
						fragColor = vec4(col,1.0);
					}

					void main() {
						mainImage(gl_FragColor, gl_FragCoord.xy);
					}
				`;
				
				uniforms = {
					iTime: { value: 0 },
					iResolution:  { value: new THREE.Vector3() },
				};
				material = new THREE.ShaderMaterial({
					fragmentShader,
					uniforms,
				});
				
				
				let geometry = new THREE.BoxGeometry();
				let material2 = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
				let cube = new THREE.Mesh(geometry, material);
				scene.add( cube );

				camera.position.z = 5;
				
				// scene.add(new THREE.Mesh(plane, material));
			}
		}
	}
	
</script>