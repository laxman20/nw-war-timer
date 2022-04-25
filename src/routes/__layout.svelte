<script lang="ts">
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';

	let intervals = $page.url.searchParams.get('intervals');

	const createTimer = function () {
		goto(`?intervals=${intervals}`);
	};
</script>

<svelte:head>
	<title>New World War Timer</title>
</svelte:head>

<h1>New World War Timer</h1>
<textarea bind:value={intervals} rows="4" cols="50"/>
<button on:click={createTimer}>Update</button>

<slot />
