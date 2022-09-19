<script lang="ts">
	import { defineComponent, type PropType } from 'vue';
	import { RouterLink } from 'vue-router';

	interface HeaderPropsLogo {
		path: string;
		alt: string;
		title: string;
	}

    export default defineComponent({
        name: "Header",
		components: {
			RouterLink
		},
		props: {
			logo: {
				type: Object as PropType<HeaderPropsLogo>,
				required: true
			}
		},
		mounted(this) {
			const headerHeight = this.$el.offsetHeight
			document.body.style.setProperty('--height-header', headerHeight + "px");
		},
	})
</script>

<template>
    <header class="header">
		<section class="container">
			<div class="header__wrapper wrapper">
				<RouterLink to="/">
					<figure class="header__container-image">
						<img 
							:src="logo.path ? logo.path : ''"
							width="40"
							height="40"
							:alt="logo?.alt"
							:title="logo?.title"
							class="header__image"
						/>
					</figure>
				</RouterLink>
				<nav class="header__navbar">
					<RouterLink class="header__navlink" to="/">Home</RouterLink>
					<RouterLink class="header__navlink" to="/pedidos">Pedidos</RouterLink>
				</nav>
			</div>
		</section>
    </header>
</template>

<style scoped> 
	header {
		background-color: #222;
		border-bottom: 4px solid #111;
		padding-block: 15px 15px;
	}
	.header__wrapper {
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.header__container-image {
		margin: 0px;
		max-width: 40px;
		height: 40px;
		width: 40px;
	}

	.header__image {
		display: inline-block;
		vertical-align: middle;
		width: 100%;
		height: auto;
	}
	.header__navlink {
		color: #fcba03;
		padding: 12px;
		transition: 0.3s ease;
	}
	.header__navlink:hover {
		color: #fff;
	}
</style>