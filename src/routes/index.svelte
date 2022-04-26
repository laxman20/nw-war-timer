<script lang="ts">
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import Textfield from '@smui/textfield';
	import HelperText from '@smui/textfield/helper-text';
	import Timer from './_Timer.svelte';

	let intervals = $page.url.searchParams.get('intervals');

	const setURL = function () {
		if (intervals) {
			const url = new URL(window.location.toString());
			url.searchParams.set('intervals', intervals);
			goto(url.href, { replaceState: true });
		}
	};
</script>

<div class="margins">
	<Textfield
		style="width: 100%;"
		helperLine$style="width: 100%;"
		textarea
		bind:value={intervals}
		label="Intervals"
	>
		<HelperText slot="helper">Enter a list of intervals in seconds to create a timer</HelperText>
	</Textfield>
</div>

<Timer bind:intervals on:start={setURL} />
