<script lang="ts">
	import './styles.css';

	import type { Filter, Todo } from '$lib/types';
	import FilterButtons from '$lib/components/FilterButtons.svelte';
	import TodoItem from '$lib/components/TodoItem.svelte';

	type Props = {
		todos?: Todo[];
	};

	let { todos = [] }: Props = $props();

	let total = $derived(todos.length);
	let completed = $derived(todos.filter((t) => t.completed).length);

	let name = $state('nuevo todo');

	let filter = $state<Filter>('all');

	let filtered = $derived(
		filter === 'active'
			? todos.filter((todo) => !todo.completed)
			: filter === 'completed'
				? todos.filter((todo) => todo.completed)
				: todos
	);

	function onsubmit() {
		const id = todos.length ? Math.max(...todos.map((t) => t.id)) + 1 : 1;
		todos.push({ id, name, completed: false });
		name = '';
	}

	let toggleAll = $state(true);
	function oncheckall() {
		for (const todo of todos) {
			todo.completed = toggleAll;
		}
		toggleAll = !toggleAll;
	}

	function removeCompleted() {
		todos = todos.filter((t) => !t.completed);
	}
</script>

<!-- Todos.svelte -->
<div class="todoapp stack-large">
	<!-- NewTodo -->
	<form {onsubmit}>
		<h2 class="label-wrapper">
			<label for="todo-0" class="label__lg"> What needs to be done? </label>
		</h2>
		<input type="text" id="todo-0" autocomplete="off" class="input input__lg" bind:value={name} />
		<button type="submit" disabled={name === ''} class="btn btn__primary btn__lg"> Add </button>
	</form>

	<!-- Filter -->
	<!-- <FilterButtons bind:filter /> -->
	<FilterButtons {filter} onfilter={(value) => (filter = value)} />

	<!-- TodosStatus -->
	<h2 id="list-heading">{completed} out of {total} items completed</h2>

	<!-- Todos -->
	<ul role="list" class="todo-list stack-large" aria-labelledby="list-heading">
		{#each filtered as todo, index (todo.id)}
			<li>
				<TodoItem
					{todo}
					onremove={(todo) => (todos = todos.filter((t) => t.id !== todo.id))}
					ontoggle={(completed) => (todo.completed = completed)}
					onupdate={(name) => (todo.name = name)}
				/>
			</li>
		{:else}
			Nothing to do
		{/each}
	</ul>

	<hr />

	<!-- MoreActions -->
	<div class="btn-group">
		<button type="button" class="btn btn__primary" onclick={oncheckall}
			>{toggleAll ? 'Check' : 'Uncheck'} all</button
		>
		<button type="button" class="btn btn__primary" onclick={removeCompleted}>
			Remove completed
		</button>
	</div>
</div>
