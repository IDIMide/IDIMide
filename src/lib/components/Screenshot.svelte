<script>
	import { onMount } from 'svelte';
	import { assets } from '$app/paths';
	import Hotspot from './Hotspot.svelte';

	export let hotspots = [];

	let activeHotspotIndex = null;
	let isMobile = false;
	let isImageVisible = false;
	let screenshotSection;

	// Check if mobile view
	function checkMobile() {
		isMobile = window.matchMedia('(max-width: 768px)').matches;
	}

	onMount(() => {
		checkMobile();
		window.addEventListener('resize', checkMobile);

		// Set up intersection observer to track image visibility
		const observer = new IntersectionObserver(
			(entries) => {
				entries.forEach(entry => {
					isImageVisible = entry.isIntersecting;
				});
			},
			{
				threshold: 0.1 // Image is visible if at least 10% is in viewport
			}
		);

		if (screenshotSection) {
			observer.observe(screenshotSection);
		}

		return () => {
			window.removeEventListener('resize', checkMobile);
			if (screenshotSection) {
				observer.unobserve(screenshotSection);
			}
		};
	});

	function handleHotspotClick(index) {
		activeHotspotIndex = activeHotspotIndex === index ? null : index;
	}

	$: activeHotspot = activeHotspotIndex !== null ? hotspots[activeHotspotIndex] : null;
</script>

<section class="screenshot-section" bind:this={screenshotSection}>
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

		{#if isMobile && isImageVisible}
			<div class="hotspot-panel" aria-live="polite">
				{#if activeHotspot}
					<h4 class="hotspot-panel-title">{activeHotspot.title}</h4>
					<p class="hotspot-panel-text">{activeHotspot.description}</p>
				{:else}
					<h4 class="hotspot-panel-title">Tap a marker to explore</h4>
					<p class="hotspot-panel-text"></p>
				{/if}
			</div>
		{/if}
	</div>
</section>
