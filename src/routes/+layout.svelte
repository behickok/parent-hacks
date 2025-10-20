<script>
	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href="/logo.png" />
	<title>Parent Hacks - Tools for Parents</title>
	<meta name="description" content="Helpful tools for parents of toddlers and young kids" />
</svelte:head>

{@render children?.()}

<style>
	:global(html) {
		box-sizing: border-box;
	}

	:global(*,
	*:before,
	*:after) {
		box-sizing: inherit;
	}

	:global(body) {
		margin: 0;
		padding: 0;
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell,
			'Open Sans', 'Helvetica Neue', sans-serif;
		background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
		min-height: 100vh;
		color: #2c3e50;
	}

	:global(h1, h2, h3, h4, h5, h6) {
		margin: 0;
	}

	:global(p) {
		margin: 0;
	}

	:global(button) {
		font-family: inherit;
	}

	:global(input) {
		font-family: inherit;
	}
</style>
