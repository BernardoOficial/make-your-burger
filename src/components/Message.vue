<script lang="ts">
	import { defineComponent } from 'vue';

	export default defineComponent({
		name: "Message",
		props: {
			message: {
				type: String,
				required: true
			}
		},
	})
</script>

<template>
	<div v-if="message" class="message">
		<p class="message__text" v-html="message"></p>
	</div>
</template>

<style scoped>
	.message {
		background-color: #fcba03;
		padding: 15px 30px;
		margin: 30px 0px;
		text-align: center;
		border: 4px solid #222;
		border-radius: 15px;
		box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.4);
	}
	.message__text,
	.message__text p {
		font-size: 16px;
		line-height: 1.6;
		letter-spacing: 0px;
		font-weight: bold;
		color: #ffffff;
	}
</style>