<script lang="ts">
	import { defineComponent, type PropType } from 'vue';

	export default defineComponent({
		name: "Banner",
		props: {
			banner: {
				type: Object as PropType<{
					path: string;
					alt: string;
					title: string;
				}>,
				required: true
			}
		},
	})
</script>

<template>
	<figure class="banner">
		<img 
			class="banner__image"
			:src="banner?.path" 
			:alt="banner?.alt"
			:title="banner?.title"
		>
		<div class="banner__container-title">
			<h2 v-if="banner.title" class="banner__title">{{ banner.title }}</h2>
			<h2 v-else class="banner__title">Sem título</h2>
		</div>
	</figure>
</template>

<style scoped>
	.banner {
		position: relative;
		margin: 0px;
		width: 100%;
		height: calc(100vh - var(--height-header) - 20vh);
	}

	.banner__image {
		width: 100%;
		height: 100%;
		object-fit: cover;
	}

	.banner__container-title {
		position: absolute;
		top: 50%;
		left: 0px;
		transform: translateY(-50%);
	}
	.banner__title {
		font-size: 60px;
		text-align: center;
		color:  #fff;
		background-color: rgba(0, 0, 0, 0.3);
		padding: 20px 40px;
	}
</style>

