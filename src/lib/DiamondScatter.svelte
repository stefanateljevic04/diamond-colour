<script>
	import { axisBottom, axisLeft, max, scaleLinear, select } from 'd3';

	let { data } = $props();
	const clarityRanks = ['I1', 'SI2', 'SI1', 'VS2', 'VS1', 'VVS2', 'VVS1', 'IF'];
	// One sequential blue ramp, from lowest to highest clarity.
	const clarityColours = ['#d7e6f5', '#b5d4e9', '#89bedc', '#5aa2cf', '#3383be', '#1764ab', '#084a91', '#08306b'];
	const colourByClarity = Object.fromEntries(clarityRanks.map((rank, i) => [rank, clarityColours[i]]));
	let containerWidth = $state(960);
	const height = 560;
	const margin = { top: 20, right: 24, bottom: 64, left: 80 };
	const width = $derived(Math.max(containerWidth, 320));
	const x = $derived(scaleLinear()
		.domain([0, max(data, (d) => d.carat) || 1]).nice()
		.range([margin.left, width - margin.right]));
	const y = $derived(scaleLinear()
		.domain([0, max(data, (d) => d.price) || 1]).nice()
		.range([height - margin.bottom, margin.top]));

	function xAxis(node) {
		$effect(() => {
			select(node).call(axisBottom(x).ticks(Math.max(2, Math.floor(width / 100))));
		});
	}

	function yAxis(node) {
		$effect(() => {
			select(node).call(axisLeft(y).ticks(8).tickFormat((value) => value.toLocaleString('en-US')));
		});
	}
</script>

<figure bind:clientWidth={containerWidth}>
	<svg viewBox="0 0 {width} {height}" role="img" aria-labelledby="scatter-title scatter-description">
		<title id="scatter-title">Diamond price by carat weight</title>
		<desc id="scatter-description">
			{data.length.toLocaleString('en-US')} diamonds, with weight in carats on the horizontal axis
			and price in US dollars on the vertical axis. Marks use a sequential blue ramp from
			I1 (lowest clarity, lightest) to IF (highest clarity, darkest), at 12% opacity.
		</desc>
		<g fill-opacity="0.12">
			{#each data as diamond}
				<circle cx={x(diamond.carat)} cy={y(diamond.price)} r="1.5" fill={colourByClarity[diamond.clarity]}>
					<title>Clarity: {diamond.clarity}; weight: {diamond.carat} carats; price: ${diamond.price.toLocaleString('en-US')}</title>
				</circle>
			{/each}
		</g>
		<g use:xAxis transform="translate(0,{height - margin.bottom})"></g>
		<g use:yAxis transform="translate({margin.left},0)"></g>
		<text x={(margin.left + width - margin.right) / 2} y={height - 16} text-anchor="middle">Weight (carats)</text>
		<text transform="translate(20,{(margin.top + height - margin.bottom) / 2}) rotate(-90)" text-anchor="middle">Price (USD)</text>
	</svg>
	<figcaption>
		<div>{data.length.toLocaleString('en-US')} stones · Linear axes</div>
		<p>Clarity: lowest → highest</p>
		<ul aria-label="Clarity legend, lowest to highest">
			{#each clarityRanks as rank}
				<li><span class="swatch" style:background-color={colourByClarity[rank]}></span>{rank}{rank === 'I1' ? ' (worst)' : rank === 'IF' ? ' (best)' : ''}</li>
			{/each}
		</ul>
	</figcaption>
</figure>

<style>
	figure { margin: 24px 0 0; width: 100%; }
	svg { display: block; width: 100%; height: auto; }
	text { fill: currentColor; font-size: 14px; }
	figcaption { margin-top: 12px; font-size: 14px; color: #52616e; }
	p { margin: 12px 0 8px; }
	ul { display: flex; flex-wrap: wrap; gap: 12px; list-style: none; padding: 0; margin: 0; }
	li { display: flex; align-items: center; gap: 5px; }
	.swatch { display: inline-block; width: 18px; height: 18px; border: 1px solid #718096; }
</style>
