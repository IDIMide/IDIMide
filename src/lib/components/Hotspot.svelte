<script>
	export let number;
	export let top;
	export let left;
	export let title;
	export let description;
	export let glowDuration;
	export let glowDelay;
	export let isActive = false;
	export let onClick;
</script>

<button
	class="hotspot"
	class:active={isActive}
	style="top: {top}; left: {left}; --glow-dur: {glowDuration}; --glow-delay: {glowDelay};"
	on:click={onClick}
	aria-label={title}
>
	<div class="hotspot-number">{number}</div>
	<div class="hotspot-tooltip">
		<h4>{title}</h4>
		<p>{description}</p>
	</div>
</button>
