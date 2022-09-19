<script lang="ts">
	import { defineComponent } from 'vue';

	export default defineComponent({
		name: "Footer",
		props: {
			copyright: {
				type: String,
				required: true,
			}			
		}
	})
</script>

<template>
    <footer class="footer">
		<section class="container">
			<div class="wrapper">
				<p v-if="copyright" class="footer__copy">{{ copyright }}</p>
				<p v-else class="footer__copy">Sem copyright</p>
			</div>
		</section>
    </footer>
</template>

<style scoped>
	.footer {
		padding: 60px 0px;
		background-color: #222;
		border-top: 4px solid #111;
		width: 100%;
	}
	.footer__copy {
		font-size: 16px;
		text-align: center;
		color: #fcba03;
	}
</style>
