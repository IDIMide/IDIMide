<script>
	import { onMount, afterUpdate } from 'svelte';

	export let number;
	export let top;
	export let left;
	export let title;
	export let description;
	export let glowDuration;
	export let glowDelay;
	export let isActive = false;
	export let onClick;

	let tooltipElement;
	let tooltipStyle = '';

	function positionTooltip() {
		if (!tooltipElement || !isActive) return;

		const rect = tooltipElement.getBoundingClientRect();
		const viewportWidth = window.innerWidth;
		const viewportHeight = window.innerHeight;
		const padding = 16; // Minimum padding from viewport edge

		let translateX = -50;
		let translateY = -50;

		// Check right edge
		if (rect.right > viewportWidth - padding) {
			translateX = -(rect.width / 2 + rect.right - viewportWidth + padding);
		}

		// Check left edge
		if (rect.left < padding) {
			translateX = -(rect.width / 2 - (padding - rect.left));
		}

		// Check bottom edge
		if (rect.bottom > viewportHeight - padding) {
			translateY = -(rect.height / 2 + rect.bottom - viewportHeight + padding);
		}

		// Check top edge
		if (rect.top < padding) {
			translateY = -(rect.height / 2 - (padding - rect.top));
		}

		tooltipStyle = `transform: translate(${translateX}%, ${translateY}%);`;
	}

	$: if (isActive) {
		// Position tooltip when it becomes active
		setTimeout(positionTooltip, 0);
	} else {
		tooltipStyle = '';
	}

	onMount(() => {
		window.addEventListener('resize', positionTooltip);
		window.addEventListener('scroll', positionTooltip);
		return () => {
			window.removeEventListener('resize', positionTooltip);
			window.removeEventListener('scroll', positionTooltip);
		};
	});

	afterUpdate(() => {
		if (isActive) {
			positionTooltip();
		}
	});
</script>

<button
	class="hotspot"
	class:active={isActive}
	style="top: {top}; left: {left}; --glow-dur: {glowDuration}; --glow-delay: {glowDelay};"
	on:click={onClick}
	aria-label={title}
>
	<div class="hotspot-number">{number}</div>
	<div class="hotspot-tooltip" bind:this={tooltipElement} style={tooltipStyle}>
		<h4>{title}</h4>
		<p>{description}</p>
	</div>
</button>
