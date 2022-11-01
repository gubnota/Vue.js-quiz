<!-- <script setup lang="ts">
import { ref } from 'vue'

defineProps<{
	msg: string
}>()

const count = ref(0)
</script> -->
<script lang="ts">
export default {
	data() {
		return {
			msg: 'hello',
			count: 1,
			elapsed: 0,
			notStarted: false,
		}
	},
	onMounted() {
		setTimeout(() => {
			this.elapsed++
		}, 1000)
	},
}
</script>
<template>
	<b-intro v-if="!notStarted"> Intro </b-intro>
</template>

<style scoped>
.read-the-docs {
	color: #888;
}
</style>
