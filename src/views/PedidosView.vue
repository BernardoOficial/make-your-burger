<script lang="ts">
	import { defineComponent } from 'vue';
	import Dashboard from '../components/Dashboard.vue';

	export default defineComponent({
    name: "Pedidos",
    components: { Dashboard }
})
</script>

<template>
	<main class="container">
		<section class="wrapper">
			<div class="container-title">
				<h1>Gerenciar pedidos</h1>
			</div>
			<Dashboard />
		</section>
	</main>
</template>
