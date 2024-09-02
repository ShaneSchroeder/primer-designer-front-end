<script>
    import "carbon-components-svelte/css/white.css";
	import { Header, HeaderNav, HeaderNavItem } from 'carbon-components-svelte';

	let isSideNavOpen = false;
</script>

<Header company="Shane Schroeder" platformName="Primer Designer" bind:isSideNavOpen>
	<HeaderNav>
		<HeaderNavItem href="/" text="Home" />
		<!-- <HeaderNavItem href="/" text="History" /> -->
	</HeaderNav>
</Header>

<main>
	<div class="container">
		<slot></slot>
	</div>
</main>

<style>
	/* Ensure the body takes up the full height */
	:global(html), :global(body) {
		margin: 0;
		padding: 0;
		height: 100%;
	}

	/* Make sure the main container doesn't overlap with the header */
	main {
		padding-top: 4rem; /* Adjust this based on the height of your header */
	}

	/* Ensure the main content area takes up the available space */
	main {
		min-height: calc(100vh - 4rem); /* Adjust based on header height */
		box-sizing: border-box;
	}

	/* Optional: Styling for the overall container */
	.container {
		padding: 1rem;
	}
</style>