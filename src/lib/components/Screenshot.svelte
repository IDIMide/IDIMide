<script>
	import { onMount } from 'svelte';
	import { assets } from '$app/paths';
	import Hotspot from './Hotspot.svelte';

	export let hotspots = [];

	let activeHotspotIndex = null;
	let isMobile = false;
	let panelScale = 1;

	// Detect actual mobile devices (not just narrow desktop browsers)
	function checkMobile() {
		const hasTouch = 'ontouchstart' in window || navigator.maxTouchPoints > 0;
		const isNarrow = window.matchMedia('(max-width: 768px)').matches;
		isMobile = hasTouch && isNarrow;
	}

	// Update panel scale to counteract zoom
	function updatePanelScale() {
		if (window.visualViewport) {
			panelScale = 1 / window.visualViewport.scale;
		}
	}

	onMount(() => {
		checkMobile();
		updatePanelScale();

		window.addEventListener('resize', checkMobile);

		if (window.visualViewport) {
			window.visualViewport.addEventListener('resize', updatePanelScale);
			window.visualViewport.addEventListener('scroll', updatePanelScale);
		}

		return () => {
			window.removeEventListener('resize', checkMobile);
			if (window.visualViewport) {
				window.visualViewport.removeEventListener('resize', updatePanelScale);
				window.visualViewport.removeEventListener('scroll', updatePanelScale);
			}
		};
	});

	function handleHotspotClick(index) {
		activeHotspotIndex = activeHotspotIndex === index ? null : index;
	}

	$: activeHotspot = activeHotspotIndex !== null ? hotspots[activeHotspotIndex] : null;
	$: panelStyle = `transform: scale(${panelScale}); transform-origin: bottom center;`;
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
						isActive={activeHotspotIndex === i}
						onClick={() => handleHotspotClick(i)}
					/>
				{/each}
			</div>
		</div>

		{#if isMobile && activeHotspot}
			<div class="hotspot-panel" style={panelStyle} aria-live="polite">
				<h4 class="hotspot-panel-title">{activeHotspot.title}</h4>
				<p class="hotspot-panel-text">{activeHotspot.description}</p>
			</div>
		{/if}
	</div>
</section>
