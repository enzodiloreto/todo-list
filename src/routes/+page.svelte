<script>
	import { onMount } from 'svelte';
	import { writable } from 'svelte/store';
	import TodoHeader from '$lib/components/TodoHeader.svelte';
	import TodoInput from '$lib/components/TodoInput.svelte';
	import TodoList from '$lib/components/TodoList.svelte';
	import TodoFooter from '$lib/components/TodoFooter.svelte';

	// Todo store
	export const todos = writable([]);
	export const filter = writable('all'); // 'all', 'active', 'completed'

	// Load todos from localStorage on mount
	onMount(() => {
		const savedTodos = localStorage.getItem('sveltekit-todos');
		if (savedTodos) {
			todos.set(JSON.parse(savedTodos));
		}
	});

	// Save todos to localStorage whenever todos change
	$: if (typeof window !== 'undefined') {
		localStorage.setItem('sveltekit-todos', JSON.stringify($todos));
	}

	// Computed values
	$: activeTodos = $todos.filter(todo => !todo.completed);
	$: completedTodos = $todos.filter(todo => todo.completed);
	$: filteredTodos = $filter === 'all' 
		? $todos 
		: $filter === 'active' 
		? activeTodos 
		: completedTodos;

	// Todo functions
	function addTodo(text) {
		const newTodo = {
			id: Date.now(),
			text: text.trim(),
			completed: false,
			createdAt: new Date().toISOString()
		};
		todos.update(list => [...list, newTodo]);
	}

	function toggleTodo(id) {
		todos.update(list => 
			list.map(todo => 
				todo.id === id 
					? { ...todo, completed: !todo.completed }
					: todo
			)
		);
	}

	function deleteTodo(id) {
		todos.update(list => list.filter(todo => todo.id !== id));
	}

	function editTodo(id, newText) {
		todos.update(list => 
			list.map(todo => 
				todo.id === id 
					? { ...todo, text: newText.trim() }
					: todo
			)
		);
	}

	function clearCompleted() {
		todos.update(list => list.filter(todo => !todo.completed));
	}

	function toggleAll() {
		const allCompleted = $todos.every(todo => todo.completed);
		todos.update(list => 
			list.map(todo => ({ ...todo, completed: !allCompleted }))
		);
	}
</script>

<svelte:head>
	<title>SvelteKit Todo App</title>
	<meta name="description" content="A beautiful todo app built with SvelteKit" />
</svelte:head>

<div class="container mx-auto px-4 py-8 max-w-2xl">
	<TodoHeader />
	
	<div class="bg-white rounded-2xl shadow-xl overflow-hidden">
		<TodoInput 
			on:addTodo={e => addTodo(e.detail)} 
			on:toggleAll={toggleAll}
			showToggleAll={$todos.length > 0}
			allCompleted={$todos.length > 0 && $todos.every(todo => todo.completed)}
		/>
		
		{#if $todos.length > 0}
			<TodoList 
				todos={filteredTodos}
				on:toggle={e => toggleTodo(e.detail)}
				on:delete={e => deleteTodo(e.detail)}
				on:edit={e => editTodo(e.detail.id, e.detail.text)}
			/>
			
			<TodoFooter 
				{filter}
				activeCount={activeTodos.length}
				hasCompleted={completedTodos.length > 0}
				on:clearCompleted={clearCompleted}
			/>
		{/if}
	</div>

	{#if $todos.length === 0}
		<div class="text-center mt-12">
			<div class="text-gray-400 text-6xl mb-4">📝</div>
			<h3 class="text-xl text-gray-600 mb-2">No todos yet</h3>
			<p class="text-gray-500">Add your first task above to get started!</p>
		</div>
	{/if}
</div>