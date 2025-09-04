<script>
	import { createEventDispatcher } from 'svelte';
	import { fade, slide } from 'svelte/transition';
	import TodoItem from './TodoItem.svelte';

	export let todos = [];
	const dispatch = createEventDispatcher();
</script>

<div class="max-h-96 overflow-y-auto">
	{#each todos as todo (todo.id)}
		<div 
			in:slide={{ duration: 200 }}
			out:slide={{ duration: 200 }}
		>
			<TodoItem 
				{todo}
				on:toggle={e => dispatch('toggle', e.detail)}
				on:delete={e => dispatch('delete', e.detail)}
				on:edit={e => dispatch('edit', e.detail)}
			/>
		</div>
	{/each}
</div>

{#if todos.length === 0}
	<div class="p-8 text-center text-gray-500" in:fade>
		<div class="text-4xl mb-2">🎯</div>
		<p>No todos match the current filter</p>
	</div>
{/if}