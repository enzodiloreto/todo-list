<script>
	import { createEventDispatcher } from 'svelte';
	import { Check, X, Edit3, Save, XCircle } from 'lucide-svelte';

	export let todo;
	
	const dispatch = createEventDispatcher();
	let isEditing = false;
	let editText = todo.text;

	function startEdit() {
		isEditing = true;
		editText = todo.text;
	}

	function saveEdit() {
		if (editText.trim() && editText !== todo.text) {
			dispatch('edit', { id: todo.id, text: editText });
		}
		isEditing = false;
	}

	function cancelEdit() {
		isEditing = false;
		editText = todo.text;
	}

	function handleKeyPress(event) {
		if (event.key === 'Enter') {
			saveEdit();
		} else if (event.key === 'Escape') {
			cancelEdit();
		}
	}

	function formatDate(dateString) {
		const date = new Date(dateString);
		return date.toLocaleDateString('en-US', { 
			month: 'short', 
			day: 'numeric',
			hour: '2-digit',
			minute: '2-digit'
		});
	}
</script>

<div class="group border-b border-gray-100 hover:bg-gray-50 transition-colors">
	<div class="flex items-center px-6 py-4 space-x-4">
		<!-- Toggle completion -->
		<button
			on:click={() => dispatch('toggle', todo.id)}
			class="flex-shrink-0 w-6 h-6 rounded-full border-2 transition-all {todo.completed 
				? 'bg-green-500 border-green-500 text-white' 
				: 'border-gray-300 hover:border-green-400'}"
		>
			{#if todo.completed}
				<Check size={14} class="mx-auto" />
			{/if}
		</button>

		<!-- Todo content -->
		<div class="flex-1 min-w-0">
			{#if isEditing}
				<input
					bind:this={editInput}
					bind:value={editText}
					on:keypress={handleKeyPress}
					on:blur={saveEdit}
					class="w-full px-2 py-1 text-lg border border-indigo-300 rounded focus:outline-none focus:border-indigo-500"
				/>
			{:else}
				<div class="flex items-center justify-between">
					<button 
						class="text-lg transition-all text-left flex-1 {todo.completed 
							? 'text-gray-500 line-through' 
							: 'text-gray-800'} hover:text-indigo-600 focus:outline-none focus:text-indigo-600"
						on:click={startEdit}
						on:keydown={(e) => e.key === 'Enter' && startEdit()}
						title="Click to edit todo"
					>
						{todo.text}
					</button>
					<span class="text-xs text-gray-400 ml-4">
						{formatDate(todo.createdAt)}
					</span>
				</div>
			{/if}
		</div>

		<!-- Actions -->
		<div class="flex items-center space-x-2 opacity-0 group-hover:opacity-100 transition-opacity">
			{#if isEditing}
				<button
					on:click={saveEdit}
					class="p-1 text-green-600 hover:bg-green-100 rounded"
					title="Save"
				>
					<Save size={16} />
				</button>
				<button
					on:click={cancelEdit}
					class="p-1 text-gray-500 hover:bg-gray-100 rounded"
					title="Cancel"
				>
					<XCircle size={16} />
				</button>
			{:else}
				<button
					on:click={startEdit}
					class="p-1 text-blue-600 hover:bg-blue-100 rounded"
					title="Edit"
				>
					<Edit3 size={16} />
				</button>
				<button
					on:click={() => dispatch('delete', todo.id)}
					class="p-1 text-red-600 hover:bg-red-100 rounded"
					title="Delete"
				>
					<X size={16} />
				</button>
			{/if}
		</div>
	</div>
</div>