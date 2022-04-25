<script lang="ts">
	import { page } from '$app/stores';
	import Timer from './_Timer.svelte';

	$: intervals = $page.url.searchParams.get('intervals');
</script>

<Timer bind:intervals />
