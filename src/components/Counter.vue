<script setup>
import CounterName from './CounterName.vue'
import ListName from './ListName.vue'
</script>

<template>
	<div class="flex flex-col h-screen p-4">
		<div ref="jams-list" class="flex-grow overflow-y-scroll">
			<ul class="max-h-full pb-4">
				<li v-for="(j, i) in jams" class="grid grid-cols-6">
					<p class="select-none font-bold">{{ i+1 }}</p>
					<list-name v-for="s in j" :skater="s"></list-name>
				</li>
			</ul>
		</div>
		<div class="pt-4 border-t">
			<main class="grid grid-cols-4 gap-2">
				<counter-name v-for="s in mainStore.jammers" :skater="s" @toggled="(s) => skaterToggled(s)"></counter-name>
				<counter-name v-for="s in mainStore.pivots" :skater="s" @toggled="(s) => skaterToggled(s)"></counter-name>
				<counter-name v-for="s in mainStore.blockers" :skater="s" @toggled="(s) => skaterToggled(s)"></counter-name>
			</main>
			<footer class="grid grid-cols-8 gap-2 mt-4">
				<button @dblclick="reset()" class="btn-reset">X</button>
				<button @click="minusOne()" class="btn-minus">-1</button>
				<a href="#/edit" class="btn-edit">E</a>
				<button @click="clear()" class="btn-clear">CLEAR</button>
				<button @click="sendToTheTrack()" class="btn-go">GO</button>
			</footer>
		</div>
	</div>
</template>

<script>
import { useMainStore } from '../store.js'
import { mapStores, mapActions } from 'pinia'
import _array from 'lodash/array'
import _collection from 'lodash/collection'

export default {
	name: "Counter",
	data() {
		return {
			selected: [],
			jams: [],
		}
	},
	computed: {
		...mapStores(useMainStore),
		reversedJams () {
			return [...this.jams].reverse()
		},
	},
	methods: {
		...mapActions(useMainStore, [
			'resetAll',
		]),
		reset () {
			this.mainStore.resetAll()
			this.selected = []
			this.jams = []
		},
		clear () {
			_collection.forEach(this.selected, function(s) {
				s.active = false
			})
			this.selected = []
		},
		sendToTheTrack () {
			_collection.forEach(this.mainStore.allOnTheTrack, function(s) {
				s.ott = false
			})
			
			if (this.selected.length === 0)
				return
			
			_collection.forEach(this.selected, function(s) {
				s.pick()
				s.active = false
			})
			this.jams.push(this.selected)
			this.selected = []
			
			setTimeout(function () {
				this.$refs["jams-list"].scrollTop = this.$refs["jams-list"].scrollHeight
			}.bind(this), 100)
		},
		minusOne () {
			if (this.selected.length === 0)
				return
			
			_collection.forEach(this.selected, function(s) {
				s.minusOne()
				s.active = false
			})
			this.selected = []
		},
		skaterToggled (s) {
			let i = _collection.find(this.selected, ['number', s.number])
			if (typeof i === "undefined")
				this.selected.push(s)
			else
				_array.remove(this.selected, (a) => { return a.number === s.number })
		},
	},
}
</script>

<style scoped>
button, a {
	@apply w-full border-0 bg-zinc-800 py-6 col-span-1 text-center;
}
.btn-reset {
	@apply bg-red-900;
}
.btn-minus {
}
.btn-edit {
	@apply bg-orange-900;
}
.btn-clear {
	@apply col-span-2 bg-zinc-700;
}
.btn-go {
	@apply col-span-3 font-bold bg-zinc-300 text-zinc-800;
}
</style>