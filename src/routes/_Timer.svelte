<script lang="ts">
	import { onMount } from 'svelte';
	import Button, { Label } from '@smui/button';

	export let intervals: string | null;

	const SECONDS_MAX = 30 * 60;

	let timer: NodeJS.Timer | null;
	let beep: HTMLAudioElement;

	$: _intervals = intervalChanged(intervals);
	$: displayedTime = toTimeString(_intervals[0]);

	onMount(() => {
		beep = new Audio('/sounds/beep.mp3');
		beep.crossOrigin = 'anonymous';
	});

	const intervalChanged = function (i: string | null): number[] {
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

	const tick = function (intrvls: number[]) {
		const currentTime = intrvls[0]--;
		displayedTime = toTimeString(currentTime);
		playSound(currentTime);
		if (intrvls[0] <= 0) {
			intrvls.shift();
		}
		if (intrvls.length == 0) {
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
		tick(intervals);
		timer = setInterval(tick, 1000, intervals);
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
	<p id="displayTimer">{displayedTime}</p>
	<div class="center">
		<Button on:click={start} variant="unelevated" class="button-shaped-round">
			<Label>Start</Label>
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
</style>
