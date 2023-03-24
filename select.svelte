<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	export let name: string;
	export let value: string | null | [] = null;
	export let placeholder: string | null = null;
	export let list: any[] = [];
	export let multiSelect: boolean = false;
	export let ShowFilter: boolean = false;
	$: getSelected = (): any[] => {
		return list?.filter((i) => {
			const val: string = i?.value;
			return value?.includes(val);
		});
	};
	let text: string;
	let open: boolean;
	let query: string = '';
	$: getResult = (): any[] => {
		return list?.filter((e) => {
			const q: string = e?.label;
			return q.indexOf(query) > -1;
		});
	};
	const dispatch = createEventDispatcher();
	const doHandleSelect = (item) => {
		open = false;
		if (multiSelect) {
			if (!value) value = [];
			if (value?.includes(item.value)) return;
			value.push(item.value);
			value = value;
		} else {
			text = item?.label;
			value = item?.value;
		}
		dispatch('change', item);
		query = '';
	};
	const doHandleRemove = (item) => {
		value = value?.filter((value) => value != item.value);
	};
</script>

<details bind:open>
	<summary>
		{#if multiSelect}
			{#each getSelected() as val}
				<input type="hidden" name={`${name}[]`} value={val.value} />
				<span class="badge"
					>{val.label} &nbsp;
					<span on:click|stopPropagation={() => doHandleRemove(val)}>x</span></span
				>
			{:else}
				<span>{text ?? placeholder ?? '-'}</span>
			{/each}
		{:else}
			<span>{text ?? placeholder ?? '-'}</span>
			<input type="hidden" {name} {value} />
		{/if}
	</summary>
	<ul role="listbox">
		{#if ShowFilter}
			<li class="search">
				<input placeholder="search..." type="text" bind:value={query} />
			</li>
		{/if}
		{#each getResult() as item}
			<li on:click={() => doHandleSelect(item)} data-value={item?.value}>{item?.label}</li>
		{/each}
	</ul>
</details>

<style>
	details {
		position: relative;
	}
	details summary {
		padding: 4px 8px;
		border-radius: 4px;
		border: 1px solid #eee;
		background: #fff;
	}
	details ul {
		position: absolute;
		left: 0;
		right: 0;
		min-height: 100px;
		max-height: 150px;
		overflow-y: auto;
		border: 1px solid #eee;
		background: #fff;
		box-shadow: 0 0 8px rgba(0, 0, 0, 0.08);
		padding: 4px;
	}
	details ul li {
		padding: 2px 4px;
	}
	details ul li:not(.search):hover {
		background: #ecec88;
	}
	.badge {
		padding: 2px 8px;
		margin: 2px;
		font-size: 0.75rem;
		border-radius: 4px;
		text-decoration: none !important;
		white-space: nowrap;
		background: #eee;
	}
</style>
