<script lang="ts">
	import { onDestroy } from 'svelte';
	import { page } from '$app/stores';

	export let intervals: string | null;

	const SECONDS_MAX = 30 * 60;

	let timer: NodeJS.Timer | null;
	let beep: HTMLAudioElement;
	let respawnTime = 0;
	let clockTime = SECONDS_MAX;

	$: _intervals = intervalChanged(intervals);

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
			respawnTime = _intervals[0];
			clockTime = SECONDS_MAX;
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
		respawnTime = intervals[0];
		clockTime = SECONDS_MAX;
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
			<p class="font-medium text-lg">Clock Time: {toTimeString(clockTime)}</p>
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
{/if}
<audio bind:this={beep}>
	<source src={$page.url.origin + '/sounds/beep.mp3'} type="audio/mpeg" />
	Your browser does not support the <code>audio</code> element.
</audio>
