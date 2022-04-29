<script lang="ts">
	import { page } from '$app/stores';
  import Timer from '$lib/components/Timer.svelte';

	let intervals = $page.url.searchParams.get('intervals');

	const textChanged = function () {
		if (intervals) {
			const url = new URL(window.location.toString());
			url.searchParams.set('intervals', intervals);
			history.replaceState({}, '', url.href);
		}
	};
</script>

<label for="intervals" class="font-medium text-gray-900">Intervals</label>
<textarea
	name="intervals"
  placeholder="10,10"
	class="
        block
        w-full
        px-3
        py-1.5
        text-base
        font-normal
        text-gray-dark
        bg-white bg-clip-padding
        border border-solid border-gray
        rounded
        transition
        ease-in-out
        m-0
        focus:text-gray-700 focus:bg-white focus:border-gray-600 focus:outline-none
      "
	bind:value={intervals}
	on:input={textChanged}
/>
<div class="text-sm text-gray-dark mt-1">
	Enter a list of intervals in seconds to create a timer
</div>

<Timer bind:intervals />
