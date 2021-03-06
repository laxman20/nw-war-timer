<script lang="ts">
	import { onDestroy } from 'svelte';
	import { page } from '$app/stores';

	export let intervals: string | null;

	const SECONDS_MAX = 30 * 60;

	let timer: NodeJS.Timer | null;
	let beep: HTMLAudioElement;
	let respawnTime = 0;
	let nextRespawnTime = 0;
	let clockTime = SECONDS_MAX;

	$: _intervals = intervalChanged(intervals);
	$: respawnClockTimes = createClockTimes(_intervals);

	onDestroy(() => {
		stop();
	});

	const intervalChanged = function (i: string | null): number[] {
		stop();
		if (!i) {
			return [];
		}
		const intervals = i
			.split(',')
			.map((s) => s.trim())
			.map((s) => parseInt(s));

		respawnTime = intervals[0];
		clockTime = SECONDS_MAX;

		return intervals;
	};

	const createClockTimes = function (intervals: number[]): number[] {
		let remaining = SECONDS_MAX;
		const result = [];
		for (let interval of intervals) {
			result.push(remaining - interval);
			remaining -= interval;
		}
		return result;
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
		nextRespawnTime = clockTime - respawnTime;
		playSound(respawnTime);
		if (intervals[0] <= 0) {
			intervals.shift();
		}
		if (intervals.length == 0) {
			stop();
			respawnTime = _intervals[0];
			clockTime = SECONDS_MAX;
		}
	};

	const playSound = function (currentTime: number) {
		if (0 < currentTime && currentTime <= 5) {
			beep.volume = 1;
			beep.play();
		}
	};

	const start = function () {
		stop();
		const intervals = [..._intervals];
		respawnTime = intervals[0];
		clockTime = SECONDS_MAX;
		beep.volume = 0;
		beep.play();
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
	<div class="container mx-auto">
		<div class="rounded overflow-hidden shadow-lg p-4 ">
			<p class="text-5xl text-center my-10">{toTimeString(respawnTime)}</p>
			<div class="flex justify-between flex-col sm:flex-row">
				<span class="font-medium text-lg text-center">Clock Time: {toTimeString(clockTime)}</span>
				<span class="font-medium text-lg text-center"
					>Next Respawn: {toTimeString(clockTime - respawnTime)}</span
				>
			</div>
		</div>
		<div class="text-center my-4">
			<button
				class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded"
				on:click={start}
			>
				{timer ? 'RESET' : 'START'}
			</button>
		</div>
	</div>
	<p class="text-lg font-bold text-gray-900">Respawn Times</p>
	<div class="flex flex-wrap gap-x-8 gap-y-1 flex-row">
		{#each respawnClockTimes as respawnClockTime}
			<span class="text-gray-900" class:font-bold={respawnClockTime === nextRespawnTime}
				>{toTimeString(respawnClockTime)}</span
			>
		{/each}
	</div>
{/if}
<audio bind:this={beep}>
	<source src={$page.url.origin + '/sounds/beep.mp3'} type="audio/mpeg" />
	Your browser does not support the <code>audio</code> element.
</audio>
