<script lang="ts">
	let input = '';
	let items: any[] = [
		'The Office',
		'Brooklyn Nine Nine',
		'Community',
		'Arrested Development',
		'The Good Place'
	];
	function addItem() {
		if (input == '' || items.includes(input)) return;
		items = [...items, input];
		input = '';
	}
	function removeItem(i: number) {
		items.splice(i, 1);
		items = items;
	}
	let ranking = false;
	let start = 0;
	let end = 0;
	let key = 1;

	$: final = start == end;
	$: middle = Math.floor((start + end) / 2);

	function array_move(arr: any[], old_index: number, new_index: number) {
		if (new_index >= arr.length) {
			var k = new_index - arr.length + 1;
			while (k--) {
				arr.push(undefined);
			}
		}
		arr.splice(new_index, 0, arr.splice(old_index, 1)[0]);
		return arr; // for testing
	}

	function search(pickedKey: boolean) {
		if (pickedKey) {
			if (final || middle == 0) {
				items = array_move(items, key, middle);
				reset();
			} else end = middle;
		} else {
			if (final) {
				items = array_move(items, key, middle + 1);
				reset();
			} else start = middle + 1;
		}
	}
	function reset() {
		start = 0;
		if (key + 1 < items.length) {
			key += 1;
			end = key - 1;
		} else {
			ranking = false;
			key = 1;
			end = 0;
		}
	}
</script>

<svelte:head>
	<link rel="stylesheet" href="https://unpkg.com/@picocss/pico@1.*/css/pico.min.css" />
</svelte:head>

<div class="layout">
	<h1 style="text-align: center;">AlgoRank</h1>
	<p>Enter Some Things You Want to Sort</p>
	<div style="display:flex">
		<input type="text" bind:value={input} />
		<button style="width: 200px; margin-left:20px" on:click={() => addItem()} disabled={ranking}
			>Add</button
		>
	</div>
	<ul>
		{#each items as item, index}
			<li
				class="item"
				class:sorted={index < key}
				class:key={index == key}
				class:middle={index == middle}
			>
				<h4 style="flex-grow:1">{item}</h4>
				<button style="width:150px" on:click={() => removeItem(index)} disabled={ranking}
					>Remove</button
				>
			</li>
		{/each}
	</ul>

	<button on:click={() => (ranking = true)} disabled={ranking}>Begin Ranking</button>
	{#if ranking}
		<div style="display:flex; text-align: center">
			<button class="left" on:click={() => search(false)}>{items[middle]}</button>
			<button class="right" on:click={() => search(true)}>{items[key]}</button>
		</div>
	{/if}
</div>

<style>
	.item {
		display: flex;
		align-items: baseline;
	}

	.layout {
		margin: 0 auto;
		padding: 20px;
		width: 500px;
	}

	.left {
		margin-right: 5px;
	}
	.right {
		margin-left: 5px;
	}
</style>
