<script>
	import { axisBottom, axisLeft, interpolatePuOr, max, scaleDiverging, scaleLinear, select } from 'd3';

	let { data } = $props();
	const residualLimit = 1.5083;
	const colourByResidual = scaleDiverging(interpolatePuOr)
		.domain([-residualLimit, 0, residualLimit]);
	const legendGradient = `linear-gradient(to right, ${Array.from({ length: 101 }, (_, i) =>
		`${colourByResidual(-residualLimit + 2 * residualLimit * i / 100)} ${i}%`).join(', ')})`;
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
			and price in US dollars on the vertical axis. Marks show log-price residuals on a
			diverging orange-to-purple scale: orange means lower price than predicted by weight,
			purple means higher price than predicted, and neutral at zero means priced as predicted.
			The symmetric colour domain is -1.5083 to +1.5083, at 12% opacity.
		</desc>
		<g fill-opacity="0.12">
			{#each data as diamond}
				<circle cx={x(diamond.carat)} cy={y(diamond.price)} r="1.5" fill={colourByResidual(diamond.price_resid)}>
					<title>Log-price residual: {diamond.price_resid.toFixed(4)} ({diamond.price_resid < 0 ? 'Lower price than predicted' : diamond.price_resid > 0 ? 'Higher price than predicted' : 'Priced as predicted'}); weight: {diamond.carat} carats; price: ${diamond.price.toLocaleString('en-US')}</title>
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
		<div class="legend" aria-label="Log-price residual colour legend">
			<p>Log-price residual (relative to weight)</p>
			<div class="colour-bar" style:background={legendGradient}></div>
			<div class="legend-labels">
				<div>−1.5083<br />Lower price than predicted</div>
				<div class="midpoint"><strong>0</strong><br />Priced as predicted</div>
				<div>+1.5083<br />Higher price than predicted</div>
			</div>
		</div>
	</figcaption>
</figure>

<style>
	figure { margin: 24px 0 0; width: 100%; }
	svg { display: block; width: 100%; height: auto; }
	text { fill: currentColor; font-size: 14px; }
	figcaption { margin-top: 12px; font-size: 14px; color: #52616e; }
	p { margin: 12px 0 8px; }
	.legend { max-width: 640px; }
	.colour-bar { position: relative; height: 18px; border: 1px solid #718096; }
	.colour-bar::after { content: ''; position: absolute; left: 50%; top: -4px; bottom: -4px; width: 2px; background: #202c36; transform: translateX(-50%); }
	.legend-labels { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin-top: 8px; }
	.midpoint { text-align: center; }
	.legend-labels > :last-child { text-align: right; }
</style>
