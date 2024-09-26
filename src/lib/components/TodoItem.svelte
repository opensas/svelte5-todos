<script lang="ts">
	import type { Todo } from '$lib/types';

	type Props = {
		todo: Todo;
		onremove?: (todo: Todo) => void;
		ontoggle?: (completed: boolean) => void;
		onupdate?: (name: string) => void;
	};

	let { todo, onremove = () => {}, ontoggle = () => {}, onupdate = () => {} }: Props = $props();

	let mode: 'display' | 'edit' = $state('display');
	let name = $state(todo.name);

	function onsubmit(e: SubmitEvent) {
		e.preventDefault();
		onupdate(name);
		mode = 'display';
	}
	function oncancel() {
		name = todo.name;
		mode = 'display';
	}
</script>

<div class="stack-small">
	{#if mode === 'display'}
		<div class="c-cb">
			<input
				type="checkbox"
				id="todo-{todo.id}"
				checked={todo.completed}
				onclick={() => ontoggle(!todo.completed)}
			/>
			<label for="todo-{todo.id}" class="todo-label">{todo.name}</label>
		</div>
		<div class="btn-group">
			<button type="button" class="btn" onclick={() => (mode = 'edit')}> Edit </button>
			<button type="button" class="btn btn__danger" onclick={() => onremove(todo)}> Delete </button>
		</div>
	{:else if mode === 'edit'}
		<form {onsubmit} class="stack-small">
			<div class="form-group">
				<label for="todo-{todo.id}" class="todo-label">
					New name for '{todo.name}'
				</label>
				<input
					bind:value={name}
					type="text"
					id="todo-{todo.id}"
					autoComplete="off"
					class="todo-text"
				/>
			</div>
			<div class="btn-group">
				<button class="btn todo-cancel" onclick={oncancel} type="button"> Cancel </button>
				<button class="btn btn__primary todo-edit" type="submit" disabled={!name}> Save </button>
			</div>
		</form>
	{/if}
</div>
