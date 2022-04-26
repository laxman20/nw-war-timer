<script lang="ts">
	import { onMount, onDestroy, createEventDispatcher } from 'svelte';
	import Button, { Label } from '@smui/button';
	import Card, { Content } from '@smui/card';

	export let intervals: string | null;

	const SECONDS_MAX = 30 * 60;
	const dispatch = createEventDispatcher();

	let timer: NodeJS.Timer | null;
	let beep: HTMLAudioElement;

	$: _intervals = intervalChanged(intervals);
	$: respawnTime = _intervals[0];
	let clockTime = SECONDS_MAX;

	onMount(() => {
		beep = new Audio('/sounds/beep.mp3');
		beep.crossOrigin = 'anonymous';
	});

	onDestroy(() => {
		stop();
	});

	const intervalChanged = function (i: string | null): number[] {
		stop();
		if (!i) {
			return [];
		}
		return i
			.split(',')
			.map((s) => s.trim())
			.map((s) => parseInt(s));
	};

	const stop = function () {
		if (timer) {
			clearInterval(timer);
			timer = null;
		}
	};

	const tick = function (intervals: number[]) {
		respawnTime = --intervals[0];
		clockTime--;
		playSound(respawnTime);
		if (intervals[0] <= 0) {
			intervals.shift();
		}
		if (intervals.length == 0) {
			stop();
		}
	};

	const playSound = function (currentTime: number) {
		if (0 < currentTime && currentTime <= 5) {
			beep.play();
		}
	};

	const start = function () {
		stop();
		const intervals = [..._intervals];
		clockTime = SECONDS_MAX;
		timer = setInterval(tick, 1000, intervals);
		dispatch('start');
	};

	const toTimeString = function (currentTime: number): string {
		const minutes = Math.floor(currentTime / 60);
		const minutePad = minutes < 10 ? '0' : '';

		const seconds = currentTime % 60;
		const secondPad = seconds < 10 ? '0' : '';
		return `${minutePad}${minutes}:${secondPad}${seconds}`;
	};
</script>

{#if _intervals.length > 0}
	<div class="card-container">
		<Card>
			<Content
				><p id="displayTimer" class="mdc-typography--headline3">
					{toTimeString(respawnTime)}
				</p></Content
			>
			<Content><p class="mdc-typography--headline6">Clock Time: {toTimeString(clockTime)}</p></Content>
		</Card>
	</div>
	<div class="center">
		<Button on:click={start} variant="unelevated" class="button-shaped-round">
			<Label>{timer ? "Reset" : "Start"}</Label>
		</Button>
	</div>
{/if}

<style>
	.center {
		display: flex;
		justify-content: center;
	}

	#displayTimer {
		text-align: center;
	}

	.card-container {
		margin-top: 15px;
		margin-bottom: 15px;
		margin-left: 150px;
		margin-right: 150px;
		background-color: var(--mdc-theme-background, #f8f8f8);
	}
</style>
