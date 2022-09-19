<script lang="ts">
	import { defineComponent } from 'vue';
	import Banner from "../components/Banner.vue";
	import Form from "../components/Form.vue";

	interface HomeData {
		Homebanner: {
			path: string;
			alt: string;
			title: string;
		}
	}

	export default defineComponent({
		name: "Home",
		components: {
			Banner,
			Form
		},
		data(): HomeData {
			return {
				Homebanner: {
					path: "../../public/images/burger.jpg",
					alt: "Make your Burguer",
					title: "Make your Burguer",
				}
			}
		}
	})
</script>

<template>
	<Banner :banner="Homebanner" />
	<main class="container">
		<section class="wrapper">
			<div class="container-title">
				<h1>Monte seu burguer</h1>
			</div>
			<Form />
		</section>
	</main>
</template>

