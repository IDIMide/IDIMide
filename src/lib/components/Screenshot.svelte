<script>
	import { onMount } from 'svelte';
	import { base, assets } from '$app/paths';
	import Hotspot from './Hotspot.svelte';

	export let hotspots = [];

	let scale = 1;
	let translateX = 0;
	let translateY = 0;
	let activeHotspotIndex = null;
	let isMobile = false;
	let viewport;
	let wrapper;
	let img;
	let maxScale = 3;

	// Touch handling
	let isPinching = false;
	let isPanning = false;
	let startDistance = 0;
	let startScale = 1;
	let startPanX = 0;
	let startPanY = 0;
	let originX = 0;
	let originY = 0;

	// Check if mobile view
	function checkMobile() {
		isMobile = window.matchMedia('(max-width: 768px)').matches;
		if (!isMobile) {
			// Reset zoom on desktop
			scale = 1;
			translateX = 0;
			translateY = 0;
		}
	}

	onMount(() => {
		checkMobile();
		window.addEventListener('resize', checkMobile);

		// Calculate max scale from image natural size
		if (img && img.complete) {
			updateMaxScale();
		}

		return () => {
			window.removeEventListener('resize', checkMobile);
		};
	});

	function updateMaxScale() {
		if (!img || !wrapper) return;
		const renderedWidth = wrapper.offsetWidth || img.clientWidth;
		if (!renderedWidth) return;
		const naturalWidth = img.naturalWidth || renderedWidth;
		const maxFromImage = naturalWidth / renderedWidth;
		maxScale = Math.max(1, Math.min(3, maxFromImage));
		if (scale > maxScale) {
			scale = maxScale;
		}
	}

	function clampTranslate() {
		if (!viewport) return;
		const viewportWidth = viewport.clientWidth;
		const viewportHeight = viewport.clientHeight;
		const contentWidth = wrapper.offsetWidth * scale;
		const contentHeight = wrapper.offsetHeight * scale;

		if (contentWidth <= viewportWidth) {
			translateX = (viewportWidth - contentWidth) / 2;
		} else {
			const minTranslateX = viewportWidth - contentWidth;
			translateX = Math.min(0, Math.max(translateX, minTranslateX));
		}

		if (contentHeight <= viewportHeight) {
			translateY = (viewportHeight - contentHeight) / 2;
		} else {
			const minTranslateY = viewportHeight - contentHeight;
			translateY = Math.min(0, Math.max(translateY, minTranslateY));
		}
	}

	function getDistance(touch1, touch2) {
		const dx = touch1.clientX - touch2.clientX;
		const dy = touch1.clientY - touch2.clientY;
		return Math.sqrt(dx * dx + dy * dy);
	}

	function getMidpoint(touch1, touch2) {
		return {
			x: (touch1.clientX + touch2.clientX) / 2,
			y: (touch1.clientY + touch2.clientY) / 2
		};
	}

	function handleTouchStart(e) {
		if (!isMobile) return;

		if (e.touches.length === 2) {
			const [touch1, touch2] = e.touches;
			isPinching = true;
			isPanning = false;
			startDistance = getDistance(touch1, touch2);
			startScale = scale;
			const midpoint = getMidpoint(touch1, touch2);
			originX = (midpoint.x - translateX) / scale;
			originY = (midpoint.y - translateY) / scale;
		} else if (e.touches.length === 1 && scale > 1) {
			const touch = e.touches[0];
			isPanning = true;
			startPanX = touch.clientX - translateX;
			startPanY = touch.clientY - translateY;
		}
	}

	function handleTouchMove(e) {
		if (!isMobile) return;

		if (e.touches.length === 2 && isPinching) {
			e.preventDefault();
			const [touch1, touch2] = e.touches;
			const distance = getDistance(touch1, touch2);
			scale = Math.min(Math.max(startScale * (distance / startDistance), 1), maxScale);
			const midpoint = getMidpoint(touch1, touch2);
			translateX = midpoint.x - originX * scale;
			translateY = midpoint.y - originY * scale;
			clampTranslate();
		} else if (e.touches.length === 1 && isPanning && scale > 1) {
			e.preventDefault();
			const touch = e.touches[0];
			translateX = touch.clientX - startPanX;
			translateY = touch.clientY - startPanY;
			clampTranslate();
		}
	}

	function handleTouchEnd(e) {
		if (!isMobile) return;

		if (e.touches.length === 0) {
			isPanning = false;
			isPinching = false;
		} else if (e.touches.length === 1) {
			const touch = e.touches[0];
			isPinching = false;
			if (scale > 1) {
				isPanning = true;
				startPanX = touch.clientX - translateX;
				startPanY = touch.clientY - translateY;
			}
		}
	}

	function handleHotspotClick(index) {
		activeHotspotIndex = activeHotspotIndex === index ? null : index;
	}

	// Calculate absolute position for a hotspot based on wrapper transform
	function getHotspotPosition(hotspot) {
		if (!wrapper || !isMobile) {
			// Desktop: use percentage positioning
			return { top: hotspot.top, left: hotspot.left };
		}

		// Mobile: calculate absolute position based on scaled/translated wrapper
		const baseWidth = wrapper.offsetWidth;
		const baseHeight = wrapper.offsetHeight;

		// Parse percentage values
		const topPercent = parseFloat(hotspot.top) / 100;
		const leftPercent = parseFloat(hotspot.left) / 100;

		// Calculate position in transformed space
		const x = baseWidth * leftPercent * scale + translateX;
		const y = baseHeight * topPercent * scale + translateY;

		return { top: `${y}px`, left: `${x}px` };
	}

	$: transform = `translate3d(${translateX}px, ${translateY}px, 0) scale(${scale})`;
	$: activeHotspot = activeHotspotIndex !== null ? hotspots[activeHotspotIndex] : null;
	// Force recalculation of hotspot positions when scale/translate changes
	$: if (scale || translateX || translateY) {
		hotspots = hotspots;
	}
</script>

<section class="screenshot-section">
	<div class="container">
		<div
			bind:this={viewport}
			class="screenshot-viewport"
			on:touchstart={handleTouchStart}
			on:touchmove={handleTouchMove}
			on:touchend={handleTouchEnd}
			on:touchcancel={() => { isPanning = false; isPinching = false; }}
		>
			<div bind:this={wrapper} class="screenshot-wrapper" style="transform: {isMobile ? transform : ''}">
				<img
					bind:this={img}
					src="{assets}/screenshot.png"
					alt="IDIMide Tracker Interface"
					class="screenshot"
					on:load={updateMaxScale}
				/>

				{#if !isMobile}
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
				{/if}
			</div>

			{#if isMobile}
				{#each hotspots as hotspot, i}
					{@const pos = getHotspotPosition(hotspot)}
					<Hotspot
						number={hotspot.number}
						top={pos.top}
						left={pos.left}
						title={hotspot.title}
						description={hotspot.description}
						glowDuration={hotspot.glowDuration}
						glowDelay={hotspot.glowDelay}
						isActive={activeHotspotIndex === i}
						onClick={() => handleHotspotClick(i)}
					/>
				{/each}
			{/if}
		</div>

		{#if isMobile}
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
