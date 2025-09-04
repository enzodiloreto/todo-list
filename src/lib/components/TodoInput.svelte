<script>
	import { createEventDispatcher } from 'svelte';
	import { Plus, CheckCheck } from 'lucide-svelte';

	export let showToggleAll = false;
	export let allCompleted = false;

	const dispatch = createEventDispatcher();
	let inputText = '';

	function handleSubmit() {
		if (inputText.trim()) {
			dispatch('addTodo', inputText);
			inputText = '';
		}
	}

	function handleKeyPress(event) {
		if (event.key === 'Enter') {
			handleSubmit();
		}
	}
</script>

<div class="p-6 border-b border-gray-200">
	<div class="flex items-center space-x-4">
		{#if showToggleAll}
			<button
				on:click={() => dispatch('toggleAll')}
				class="flex-shrink-0 p-2 rounded-lg transition-colors {allCompleted 
					? 'text-green-600 bg-green-100 hover:bg-green-200' 
					: 'text-gray-400 hover:text-gray-600 hover:bg-gray-100'}"
				title={allCompleted ? 'Mark all as incomplete' : 'Mark all as complete'}
			>
				<CheckCheck size={20} />
			</button>
		{/if}

		<div class="flex-1 relative">
			<input
				bind:value={inputText}
				on:keypress={handleKeyPress}
				placeholder="What needs to be done?"
				class="w-full text-lg px-4 py-3 border-2 border-gray-200 rounded-xl focus:border-indigo-500 focus:outline-none transition-colors"
			/>
		</div>

		<button
			on:click={handleSubmit}
			disabled={!inputText.trim()}
			class="flex-shrink-0 bg-indigo-600 text-white p-3 rounded-xl hover:bg-indigo-700 disabled:bg-gray-300 disabled:cursor-not-allowed transition-colors"
		>
			<Plus size={20} />
		</button>
	</div>
</div>
