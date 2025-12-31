<script>
	import { onMount } from 'svelte';
	import { assets } from '$app/paths';
	import Hotspot from './Hotspot.svelte';

	export let hotspots = [];

	let isMobile = false;
	let highlightedTile = null;

	// Detect touch devices (tablets and phones in any orientation)
	function checkMobile() {
		isMobile = 'ontouchstart' in window || navigator.maxTouchPoints > 0;
	}

	// Scroll to and highlight the corresponding tile
	function scrollToTile(index) {
		if (!isMobile) return;

		const tileId = `hotspot-tile-${index}`;
		const element = document.getElementById(tileId);

		if (element) {
			element.scrollIntoView({ behavior: 'smooth', block: 'nearest' });

			// Highlight the tile briefly
			highlightedTile = index;
			setTimeout(() => {
				highlightedTile = null;
			}, 2000);
		}
	}

	onMount(() => {
		checkMobile();
	});
</script>

<section class="screenshot-section">
	<div class="container">
		<div class="screenshot-viewport">
			<div class="screenshot-wrapper">
				<img
					src="{assets}/screenshot.png"
					alt="IDIMide Tracker Interface"
					class="screenshot"
				/>

				{#each hotspots as hotspot, i}
					<Hotspot
						number={hotspot.number}
						top={hotspot.top}
						left={hotspot.left}
						title={hotspot.title}
						description={hotspot.description}
						glowDuration={hotspot.glowDuration}
						glowDelay={hotspot.glowDelay}
						isActive={false}
						onClick={() => scrollToTile(i)}
					/>
				{/each}
			</div>
		</div>

		{#if isMobile}
			<div class="hotspot-tiles">
				{#each hotspots as hotspot, i}
					<div
						id="hotspot-tile-{i}"
						class="hotspot-tile"
						class:highlighted={highlightedTile === i}
					>
						<div class="hotspot-tile-number">{hotspot.number}</div>
						<div class="hotspot-tile-content">
							<h4>{hotspot.title}</h4>
							<p>{hotspot.description}</p>
						</div>
					</div>
				{/each}
			</div>
		{/if}
	</div>
</section>
