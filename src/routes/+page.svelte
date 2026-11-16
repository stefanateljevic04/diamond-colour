<script>
	import { onMount } from 'svelte';
	import { csv } from 'd3';
	import DiamondScatter from '#lib/DiamondScatter.svelte';

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
	{/if}
</main>

<style>
	:global(body) { margin: 0; font-family: system-ui, sans-serif; color: #202c36; background: #fff; }
	main { max-width: 1100px; margin: 0 auto; padding: 32px 16px; }
	h1 { margin: 0 0 12px; }
	p { line-height: 1.5; }
</style>
