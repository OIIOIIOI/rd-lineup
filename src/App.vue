<template>
	<component :is="currentView" />
</template>

<script>
import Builder from "./components/Builder.vue";
import Counter from "./components/Counter.vue";
import EditView from "./components/EditView.vue";
import Rules from "./components/Rules.vue";

const routes = {
	'/': Counter,
	'/builder': Builder,
	'/edit': EditView,
	'/rules': Rules,
}

export default {
	data() {
		return {
			currentPath: window.location.hash,
		}
	},
	computed: {
		currentView() {
			return routes[this.currentPath.slice(1) || '/'] || Builder
		},
	},
	methods: {},
	mounted() {
		window.addEventListener('hashchange', () => {
			this.currentPath = window.location.hash
		})
	}
}
</script>

<style>
html, body {
	@apply bg-zinc-900 text-zinc-100;
}
#app {
	@apply my-0 mx-auto text-base;
}
</style>
