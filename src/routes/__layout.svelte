<script lang="ts">
	import TopAppBar, { Row, Section, Title, AutoAdjust } from '@smui/top-app-bar';
	import type { TopAppBarComponentDev } from '@smui/top-app-bar';

	let topAppBar: TopAppBarComponentDev;
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
		<slot />
	</div>
</AutoAdjust>
