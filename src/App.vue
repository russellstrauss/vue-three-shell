<template>
	<div id="app">
		<Shader msg="Welcome to Your Vue.js App"/>
	</div>
</template>

<script>

	import Shader from './components/Shader.vue'

	export default {
		
		name: 'App',
		
		components: {
			Shader
		}
	}
	
</script>

<style>
	body {
		padding: 0;
		margin: 0;
		overflow: hidden;
	}
</style>