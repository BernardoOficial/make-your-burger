
<script lang="ts">
	import { RouterView } from 'vue-router'
	import { defineComponent } from 'vue';
	import Header from './components/Header.vue'
	import Footer from './components/Footer.vue'

	interface AppData {
		logo: {
			path: string;
			alt: string;
			title: string;
		}
	}

	export default defineComponent({
		name: "App",
		components: {
			Header,
			Footer,
			RouterView
		},
		data(): AppData {
			return {
				logo: {
					path: "../../public/images/logo.png",
					alt: "Make your Burguer",
					title: "Sua melhor hamburgueria",
				}
			}
		}
	})
</script>

<template>
	<section>
		<Header :logo="logo" />
		<RouterView />
		<Footer copyright="Make Your Burguer &copy; 2022" />
	</section>
</template>