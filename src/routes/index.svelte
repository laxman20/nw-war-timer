<script lang="ts">
	import { page } from '$app/stores';
	import Textfield from '@smui/textfield';
	import HelperText from '@smui/textfield/helper-text';
	import Timer from './_Timer.svelte';

	let intervals = $page.url.searchParams.get('intervals');

	const setURL = function () {
		if (intervals) {
			const url = new URL(window.location.toString());
			url.searchParams.set('intervals', intervals);
			history.replaceState({}, '', url.href);
		}
	};

	const textChanged = function() {
		setURL();
	}
</script>

<div class="margins">
	<Textfield
		style="width: 100%;"
		helperLine$style="width: 100%;"
		bind:value={intervals}
		on:input={textChanged}
		label="Intervals"
	>
		<HelperText persistent slot="helper">Enter a list of intervals in seconds to create a timer</HelperText>
	</Textfield>
</div>

<Timer bind:intervals />
