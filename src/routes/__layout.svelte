<script lang="ts">
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import TopAppBar, { Row, Section, Title, AutoAdjust } from '@smui/top-app-bar';
	import type { TopAppBarComponentDev } from '@smui/top-app-bar';
	import Textfield from '@smui/textfield';
	import HelperText from '@smui/textfield/helper-text';
	import Button, { Label } from '@smui/button';

	let topAppBar: TopAppBarComponentDev;

	let intervals = $page.url.searchParams.get('intervals');

	const createTimer = function () {
		goto(`?intervals=${intervals}`);
	};
</script>

<svelte:head>
	<title>New World War Timer</title>
</svelte:head>

<TopAppBar bind:this={topAppBar} variant="static" color="secondary">
	<Row>
		<Section>
			<Title>New World War Timer</Title>
		</Section>
	</Row>
</TopAppBar>

<AutoAdjust {topAppBar}>
	<div class="container">
		<div class="margins">
			<Textfield
				style="width: 100%;"
				helperLine$style="width: 100%;"
				textarea
				bind:value={intervals}
				label="Intervals"
			>
				<HelperText slot="helper">Enter a list of intervals in seconds to create a timer</HelperText
				>
			</Textfield>
		</div>

		<Button on:click={createTimer} variant="unelevated" class="button-shaped-round">
			<Label>Update</Label>
		</Button>
		<slot />
	</div>
</AutoAdjust>
