<script>
	import { createEventDispatcher } from 'svelte';
	let clicked = false;
	const dispatch = createEventDispatcher();
	let search = '';

	function handleInput(e) {
		if (e.key === 'Enter' && search !== '') {
			if (clicked) {
				onSearch();
				document.activeElement.blur();
			} else {
				clicked = true;
				document.activeElement.blur();
			}
		}
	}

	function handleClick() {
		clicked = true;
		onSearch();
		document.activeElement.blur();
	}

	function onSearch() {
		dispatch('search', { search: search });
		search = '';
	}
</script>

<div
	on:transitionend={onSearch}
	id="search"
	class="input-position my-4"
	style={`top: ${clicked ? '0' : '45%'}`}
>
	<div class="input-wrapper">
		<span role="img" aria-label="Place" class="material-icons">place</span>
		<input
			bind:value={search}
			on:keydown={handleInput}
			type="text"
			placeholder="Enter city or zip code..."
		/>
		<button on:click={handleClick} type="button">
			<span class="material-icons">search</span>
		</button>
	</div>
</div>

<style>
	.input-position {
		position: absolute;
		height: 5rem;
		background-color: white;
		border-radius: 0.75rem;
		transition: all 500ms ease;
		width: min(100%, 65ch);
	}

	.input-wrapper {
		--spacing: 0.5rem;
		display: grid;
		grid-template-columns: auto 1fr auto;
		height: 100%;
		gap: var(--spacing);
		padding-inline: var(--spacing);
	}

	input {
		min-width: 0;
		font-size: var(--step-1);
		font-weight: 600;
	}

	input::placeholder {
		font-size: var(--step-1);
	}

	input:focus {
		outline: none;
	}

	span {
		align-self: center;
		font-size: 2rem;
	}

	span[role='img'] {
		color: #ea4335;
	}

	span:last-of-type {
		cursor: pointer;
	}
</style>
