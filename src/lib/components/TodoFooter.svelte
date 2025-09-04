<script>
	import { createEventDispatcher } from 'svelte';

	export let filter;
	export let activeCount;
	export let hasCompleted;

	const dispatch = createEventDispatcher();
	
	const filters = [
		{ key: 'all', label: 'All' },
		{ key: 'active', label: 'Active' },
		{ key: 'completed', label: 'Completed' }
	];

	function setFilter(filterKey) {
		filter.set(filterKey);
	}
</script>

<div class="px-6 py-4 bg-gray-50 border-t border-gray-200">
	<div class="flex items-center justify-between">
		<!-- Item count -->
		<span class="text-sm text-gray-600">
			{activeCount} {activeCount === 1 ? 'item' : 'items'} left
		</span>

		<!-- Filter buttons -->
		<div class="flex space-x-1 bg-white rounded-lg border border-gray-200 p-1">
			{#each filters as filterOption}
				<button
					on:click={() => setFilter(filterOption.key)}
					class="px-3 py-1 text-sm rounded transition-all {$filter === filterOption.key
						? 'bg-indigo-600 text-white shadow-sm'
						: 'text-gray-600 hover:text-gray-800 hover:bg-gray-100'}"
				>
					{filterOption.label}
				</button>
			{/each}
		</div>

		<!-- Clear completed -->
		{#if hasCompleted}
			<button
				on:click={() => dispatch('clearCompleted')}
				class="text-sm text-gray-600 hover:text-red-600 transition-colors"
			>
				Clear completed
			</button>
		{:else}
			<div class="w-24"></div> <!-- Spacer to maintain layout -->
		{/if}
	</div>
</div>