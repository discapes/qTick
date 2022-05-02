<script>
	import Slider from "@bulatdashiev/svelte-slider";
	import ColorPicker from "./ColorPicker.svelte";
	import Pie from "./Pie.svelte";
	const size = 400;
	const inset = 29;
	const thickness = 50;

	const twoDigits = (n) => (n > 9 ? Math.floor(n) : "0" + Math.floor(n));
	function HHMM(h) {
		let H = Math.floor(h);
		let M = (h * 60) % 60;
		return `${twoDigits(H)}:${twoDigits(M)}`;
	}
	const parseHHMM = (s) => +s.split(":")[0] + +s.split(":")[1] / 60;

	const TimeObject = (name, color, range) => ({
		name,
		color,
		range,
		get start() {
			console.log;
			return this.range[0] / 24;
		},
		get end() {
			return this.range[1] / 24;
		},
	});

	let weekdays = ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"];
	let weekday = "Mon";
	let tobjs;
	function loadTobjs() {
		let local = localStorage.getItem("tobjs-" + weekday);
		if (local) tobjs = JSON.parse(local).map(tobj => TimeObject(tobj.name, tobj.color, tobj.range));
		if (!tobjs)
			tobjs = [
				TimeObject("sleep", "blue", [0 + 0 / 60, 7 + 0 / 60]),
				TimeObject("school", "red", [8 + 30 / 60, 14 + 45 / 60]),
				TimeObject("swimming", "green", [17 + 20 / 60, 19 + 15 / 60]),
			];
	}
	function saveTobjs(tobjs) {
		localStorage.setItem('tobjs-' + weekday, JSON.stringify(tobjs));
	}
	$: weekday, loadTobjs();
	$: saveTobjs(tobjs);
</script>

<div
	class="
	 bg-gradient-to-t from-primary-900 to-secondary-900
	 center
	 min-h-screen
	 overflow-auto"
>
	<div class="w-full max-w-xl pb-5 flex flex-col gap-3">
		<h1 class="text-center">Time visualizer</h1>
		<div class="flex gap-3">
			{#each weekdays as day}
				<button
					class={`w-full bg-neutral-100/10 ${
						weekday === day
							? "bg-transparent"
							: "hover:bg-neutral-100/20"
					}`}
					on:click={() => (weekday = day)}>{day}</button
				>
			{/each}
		</div>
		<br />
		{#each tobjs as tobj, i}
			<div>
				<div class="flex gap-3">
					<input
						class="bg-neutral-100/10 pl-1 w-32 hover:bg-neutral-100/20"
						bind:value={tobj.name}
					/>
					<div>
						<input
							class="bg-neutral-100/10 hover:bg-neutral-100/20"
							type="time"
							value={HHMM(tobj.start * 24)}
							on:input={(e) =>
								(tobj.range[0] = parseHHMM(e.target.value))}
						/>
						-
						<input
							class="bg-neutral-100/10 p.1 hover:bg-neutral-100/20"
							type="time"
							value={HHMM(tobj.end * 24)}
							on:input={(e) =>
								(tobj.range[1] = parseHHMM(e.target.value))}
						/>
					</div>
					<ColorPicker bind:value={tobj.color} />
					<button
						class="hover:bg-neutral-100/20 bg-neutral-100/10 h-[24px] w-[24px]"
						on:click={() => {
							tobjs.splice(i, 1);
							tobjs = tobjs;
						}}>X</button
					>
				</div>
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
						<span slot="left" style="font-size: 20px;">&#9193;</span
						>
						<span slot="right" style="font-size: 20px;"
							>&#9194;</span
						>
					</Slider>
				</div>
			</div>
		{/each}

		<div class="flex">
			<div
				class="clock center"
				style={`min-width: ${size}px; min-height: ${size}px`}
			>
				<span>
					<Pie size={size - inset} {tobjs} {thickness} />
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
				<button
					class="bg-neutral-100/10 p-1 rounded border-2 m-2"
					on:click={() =>
						(tobjs = [...tobjs, TimeObject("", "", [0, 0])])}
					>new</button
				>
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
	circle {
		mix-blend-mode: lighten;
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
