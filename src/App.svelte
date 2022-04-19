<script>
	import Slider from "@bulatdashiev/svelte-slider";
	import { spring, tweened } from "svelte/motion";
	import Pie from "./Pie.svelte";
	const size = 400;
	const inset = 29;
	const thickness = 50;
	const root = document.documentElement;

	const twoDigits = (n) => (n > 9 ? Math.floor(n) : "0" + Math.floor(n));
	const HHMM = (h) => {
		let H = Math.floor(h);
		let M = (h * 60) % 60;
		return `${twoDigits(H)}:${twoDigits(M)}`;
	};

	const TimeObject = (name, color, range) => ({
		name,
		color,
		range,
		offset: 0,
		get startTime() {
			return this.range[0] % 24;
		},
		get endTime() {
			return this.range[1] % 24;
		},
		get start() {
			return ((this.range[0] + this.offset) % 24) / 24;
		},
		get end() {
			return ((this.range[1] + this.offset) % 24) / 24;
		},
	});

	function rotateTo0(tobjs) {
		let offset = 0;
		for (let tobj of tobjs) {
			if (tobj.startTime > tobj.endTime) {
				offset = 24 - tobj.startTime;
			}
		}
		for (let tobj of tobjs) tobj.offset = offset;
		return -offset;
	}

	let tobjs = [TimeObject("test", "blue", [0, 16])];
	let offset = 0;
	$: offset = rotateTo0(tobjs);
</script>

<div
	class="
	 bg-gradient-to-t from-primary-900 to-secondary-900
	 center
	 h-screen
	 overflow-auto"
>
	<div class="w-full max-w-xl">
		<h1 class="text-center">Time visualizer</h1>

		{#each tobjs as tobj}
			{tobj.name}:
			{HHMM(tobj.startTime)} -
			{HHMM(tobj.endTime)}
			<div
				class="w-full"
				style={`--progress-bg: ${
					tobj.range[0] <= tobj.range[1] ? "#ff9355" : "white"
				};
			--track-bg: ${tobj.range[0] <= tobj.range[1] ? "white" : "#ff9355"};
			`}
			>
				<Slider
					step="0.25"
					bind:value={tobj.range}
					max="24"
					range={tobj.range}
				>
					<span slot="left" style="font-size: 20px;">&#9193;</span>
					<span slot="right" style="font-size: 20px;">&#9194;</span>
				</Slider>
			</div>
		{/each}

		<div class="flex">
			<div
				class="clock center"
				style={`width: ${size}px; height: ${size}px`}
			>
				<span style="opacity:0.5">
					<Pie
						size={size - inset}
						{tobjs}
						rotate={270 + (offset / 24) * 360}
						{thickness}
					/>
				</span>
			</div>
			<div>
				{#each tobjs as tobj, i}
					{#if tobjs.findIndex((e) => e.name == tobj.name) == i}
						<div class="flex items-center text-xl">
							<span
								style={`background: ${tobj.color};`}
								class="inline-block w-8 h-8 m-1 border-2 rounded"
							/>
							{tobj.name}
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
	:root {
		font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
			Oxygen-Sans, Ubuntu, Cantarell, "Helvetica Neue", sans-serif;
		color: white;
		--thumb-bg: transparent;
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
