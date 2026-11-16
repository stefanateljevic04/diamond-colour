<script>
	import { onMount } from 'svelte';
	import { csv } from 'd3';
	import DiamondScatter from '#lib/DiamondScatter.svelte';
	import ClarityScatter from '#lib/ClarityScatter.svelte';

	let diamonds = $state.raw([]);
	let loading = $state(true);
	let error = $state('');

	onMount(() => {
		const controller = new AbortController();
		const numericColumns = ['carat', 'price', 'depth', 'table', 'x', 'y', 'z', 'price_resid'];
		csv('/diamonds.csv', { signal: controller.signal }, (row) => {
			for (const column of numericColumns) row[column] = Number(row[column]);
			return row;
		})
			.then((rows) => { diamonds = rows; })
			.catch((cause) => {
				if (cause.name !== 'AbortError') error = 'Unable to load diamond data. Please reload the page.';
			})
			.finally(() => { loading = false; });
		return () => controller.abort();
	});
</script>

<svelte:head>
	<title>Diamond weight and price</title>
	<meta name="description" content="Carat weight and price for 53,940 diamonds." />
</svelte:head>

<main>
	<h1>Diamond weight and price</h1>
	<p>Each point represents one diamond. Low opacity reveals where stones overlap.</p>
	{#if loading}
		<p role="status">Loading diamonds…</p>
	{:else if error}
		<p role="alert">{error}</p>
	{:else}
		<DiamondScatter data={diamonds} />
		<section id="clarity-comparison" aria-labelledby="clarity-heading">
			<h2 id="clarity-heading">Rainbow, measured: clarity comparison</h2>
			<p>All {diamonds.length.toLocaleString('en-US')} diamonds, in the same draw order. Every clarity plot uses identical linear domains, axes, 1.5-unit point radius and 12% opacity. Only the colormap changes: D3 Blues versus D3 Turbo.</p>
			<p><a href="#clarity-colour">Colour comparison</a> · <a href="#clarity-greyscale">Greyscale comparison</a></p>
			<h2 id="clarity-colour">Original colour versions</h2>
			<div class="comparison">
				<ClarityScatter data={diamonds} chartId="clarity-blue" />
				<ClarityScatter data={diamonds} rainbow chartId="clarity-rainbow" />
			</div>
			<h2 id="clarity-greyscale">Greyscale versions</h2>
			<p>The same plots and legend swatches with a 100% greyscale filter. Read each legend from I1 (worst) to IF (best) to inspect whether lightness changes consistently with rank. Swatches show the full-opacity colours; points retain 12% opacity.</p>
			<div class="comparison">
				<ClarityScatter data={diamonds} greyscale chartId="clarity-blue-grey" />
				<ClarityScatter data={diamonds} rainbow greyscale chartId="clarity-rainbow-grey" />
			</div>
		</section>
	{/if}
</main>

<style>
	:global(body) { margin: 0; font-family: system-ui, sans-serif; color: #202c36; background: #fff; }
	main { max-width: 1100px; margin: 0 auto; padding: 32px 16px; }
	h1 { margin: 0 0 12px; }
	p { line-height: 1.5; }
	section { margin-top: 64px; }
	h2 { margin-top: 32px; scroll-margin-top: 20px; }
	.comparison { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 24px; min-width: 760px; }
	section { overflow-x: auto; padding-bottom: 16px; }
	a { color: #245b88; }
</style>
