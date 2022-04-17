<template>
	<p :class="positionClass" class="select-none">{{ skater.number }}</p>
</template>

<script>
import { Position } from "../store";

export default {
	name: "ListName",
	props: {
		skater: Object,
	},
	data() {
		return {}
	},
	computed: {
		positionClass () {
			switch (this.skater.position) {
				case Position.Jammer:
					return 'text-teal-400'
				case Position.Pivot:
					return 'text-purple-400'
				default:
					return ''
			}
		},
	},
}
</script>

<style scoped>

</style>