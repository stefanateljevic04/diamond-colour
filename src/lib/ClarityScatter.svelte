<script>
	import { axisBottom, axisLeft, interpolateBlues, interpolateTurbo, max, scaleLinear, select } from 'd3';

	let { data, rainbow = false, greyscale = false, chartId } = $props();
	const grades = ['I1', 'SI2', 'SI1', 'VS2', 'VS1', 'VVS2', 'VVS1', 'IF'];
	const width = 520;
	const height = 420;
	const margin = { top: 16, right: 20, bottom: 56, left: 68 };
	const opacity = 0.12;
	const radius = 1.5;
	let canvas;
	const palette = $derived(grades.map((grade, index) =>
		(rainbow ? interpolateTurbo : interpolateBlues)(index / (grades.length - 1))));
	const x = $derived(scaleLinear().domain([0, max(data, (d) => d.carat) || 1]).nice()
		.range([margin.left, width - margin.right]));
	const y = $derived(scaleLinear().domain([0, max(data, (d) => d.price) || 1]).nice()
		.range([height - margin.bottom, margin.top]));

	$effect(() => {
		if (!canvas) return;
		const context = canvas.getContext('2d');
		const pixelRatio = window.devicePixelRatio || 1;
		canvas.width = width * pixelRatio;
		canvas.height = height * pixelRatio;
		context.scale(pixelRatio, pixelRatio);
		context.clearRect(0, 0, width, height);
		context.globalAlpha = opacity;
		for (const diamond of data) {
			context.fillStyle = palette[grades.indexOf(diamond.clarity)];
			context.beginPath();
			context.arc(x(diamond.carat), y(diamond.price), radius, 0, 2 * Math.PI);
			context.fill();
		}
	});

	function xAxis(node) {
		$effect(() => { select(node).call(axisBottom(x).ticks(5)); });
	}

	function yAxis(node) {
		$effect(() => {
			select(node).call(axisLeft(y).ticks(6).tickFormat((value) => value.toLocaleString('en-US')));
		});
	}
</script>

<figure aria-labelledby={chartId}>
	<h3 id={chartId}>{rainbow ? 'Rainbow (Turbo)' : 'Sequential blue'}{greyscale ? ' — greyscale' : ''}</h3>
	<div class="plot" role="img" aria-label={`${data.length.toLocaleString('en-US')} diamonds. Carat versus price, coloured by clarity from I1 (worst) to IF (best), using ${rainbow ? 'Turbo rainbow' : 'sequential blue'}${greyscale ? ', converted to greyscale' : ''}.`}>
		<canvas bind:this={canvas} class:greyscale></canvas>
		<svg viewBox="0 0 {width} {height}" aria-hidden="true">
			<g use:xAxis transform="translate(0,{height - margin.bottom})"></g>
			<g use:yAxis transform="translate({margin.left},0)"></g>
			<text x={(margin.left + width - margin.right) / 2} y={height - 12} text-anchor="middle">Weight (carats)</text>
			<text transform="translate(16,{(margin.top + height - margin.bottom) / 2}) rotate(-90)" text-anchor="middle">Price (USD)</text>
		</svg>
	</div>
	<figcaption>
		<p>Clarity · worst → best</p>
		<div class="legend">
			{#each grades as grade, index}
				<div><span class="swatch" class:greyscale style:background={palette[index]}></span><span>{grade}</span></div>
			{/each}
		</div>
		<div class="endpoints"><span>I1 · worst</span><span>IF · best</span></div>
	</figcaption>
</figure>

<style>
	figure { margin: 0; min-width: 0; }
	h3 { margin: 0 0 12px; font-size: 17px; }
	.plot { position: relative; aspect-ratio: 520 / 420; background: white; }
	canvas, svg { position: absolute; inset: 0; width: 100%; height: 100%; display: block; }
	.greyscale { filter: grayscale(1); }
	text { fill: currentColor; font-size: 13px; }
	figcaption { font-size: 13px; }
	p { margin: 8px 0; }
	.legend { display: grid; grid-template-columns: repeat(8, 1fr); gap: 4px; }
	.legend > div { display: grid; gap: 5px; text-align: center; }
	.swatch { height: 20px; border: 1px solid #adb5bd; }
	.endpoints { display: flex; justify-content: space-between; margin-top: 8px; }
</style>
