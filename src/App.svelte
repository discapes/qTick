<script>
	import Slider from "@bulatdashiev/svelte-slider";
	import { spring, tweened } from "svelte/motion";
	import Pie from "./Pie.svelte";

	const twoDigits = (n) => (n > 9 ? Math.floor(n) : "0" + Math.floor(n));
	const hoursToHHMM = (h) => {
		let H = Math.floor(h);
		let M = (h * 60) % 60;
		return `${twoDigits(H)}:${twoDigits(M)}`;
	};
	const HMToHours = (h, m) => h + m * 60;

	const TimeObj = (name, color, start, end) => ({
		name,
		color,
		start: start / 24,
		end: end / 24,
	});
	const size = 400;
	const inset = 29;
	const thickness = 50;

	const stuff = [
		// tuesday
		TimeObj("sleep", "blue", 		0+0		/60, 7+0	/60),
		TimeObj("morning", "firebrick",	7+0		/60, 7+45	/60),
		TimeObj("car", "darkorange",	7+45	/60, 8+25 	/60),
		TimeObj("school", "red", 		8+30	/60, 14+45	/60),
		TimeObj("eating", "hotpink", 	15+0	/60, 15+30	/60),

		TimeObj("free", "lime", 		15+30	/60, 16+45	/60),

		TimeObj("car", "darkorange", 	16+50	/60, 17+20	/60),
		TimeObj("swimming", "green", 	17+20	/60, 19+15	/60),
		TimeObj("car", "darkorange", 	19+15	/60, 19+45	/60),

		TimeObj("free", "lime", 		20+0	/60, 22+0	/60),
		TimeObj("eating", "hotpink", 	22+0	/60, 22+15	/60),
		TimeObj("free", "lime", 		22+15	/60, 23+40	/60),
		// monday
		// TimeObj("sleep", "blue", 		0+0		/60, 7+0	/60),
		// TimeObj("morning", "firebrick",	7+0		/60, 7+45	/60),
		// TimeObj("car", "darkorange",	7+45	/60, 8+25 	/60),
		// TimeObj("walking", "cyan",		8+25	/60, 8+30 	/60),
		// TimeObj("school", "red", 		8+30	/60, 16+5	/60),
		// TimeObj("car", "darkorange",	16+5	/60, 16+45 	/60),
		// TimeObj("walking", "cyan",		16+45	/60, 16+55 	/60),
		// TimeObj("eating", "hotpink", 	17+0	/60, 17+30	/60),

		// TimeObj("free", "lime", 		17+30	/60, 18+15	/60),

		// TimeObj("car", "darkorange", 	18+15	/60, 18+45	/60),
		// TimeObj("swimming", "green", 	18+45	/60, 20+45	/60),
		// TimeObj("car", "darkorange", 	20+45	/60, 21+15	/60),

		// TimeObj("free", "lime", 		21+15	/60, 22+0	/60),
		// TimeObj("eating", "hotpink", 	22+0	/60, 22+15	/60),
		// TimeObj("free", "lime", 		22+15	/60, 24+0	/60),
	];

	let offset = 0;
	for (let thing of stuff) {
		if (thing.start > thing.end) {
			offset = 24 - thing.start * 24;
		}
	}
	for (let thing of stuff) {
		thing.start += offset / 24;
		thing.end += offset / 24;
		if (thing.start == 24 / 24) thing.start = 0;
	}
	let slider = [offset * 4, 24 * 4];
	$: offset = slider[0] / 4;
</script>

<div
	class="
	 bg-gradient-to-t from-primary-500 to-secondary-500
	 center
	 h-screen
	 overflow-auto"
>
	<div class="w-full max-w-xl">
		<h1>Time visualizer</h1>

		offset: -{hoursToHHMM(offset)}
		<div class="text-left w-full">
			<Slider max={24 * 4} bind:value={slider} />
		</div>

		<div class="flex">
			<div
				class="clock center"
				style={`width: ${size}px; height: ${size}px`}
			>
				<span style="opacity:0.5">
					<Pie
						size={size - inset}
						{stuff}
						rotate={270 - (offset / 24) * 360}
						{thickness}
					/>
				</span>
			</div>
			<div>
				{#each stuff as thing, i}
					{#if stuff.findIndex((e) => e.name == thing.name) == i}
						<div class="flex items-center text-xl">
							<span
								style={`background: ${thing.color};`}
								class="inline-block w-8 h-8 m-1 border-2 rounded"
							/>
							{thing.name}
						</div>
					{/if}
				{/each}
			</div>
		</div>
	</div>
</div>

<style global lang="postcss">
	@tailwind base;
	@tailwind components;
	@tailwind utilities;

	h1 {
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}
	html {
		font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
			Oxygen-Sans, Ubuntu, Cantarell, "Helvetica Neue", sans-serif;
		text-align: center;
		color: white;
	}
	* {
		border-color: white;
	}

	.center {
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.clock {
		background-image: url("https://www.columbia.edu/~njn2118/journal/2018/9/img/clockface.png");
		background-size: contain;
	}
</style>
