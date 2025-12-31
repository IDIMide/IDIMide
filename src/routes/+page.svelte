<script>
	import Hero from '$lib/components/Hero.svelte';
	import Screenshot from '$lib/components/Screenshot.svelte';
	import Subscribe from '$lib/components/Subscribe.svelte';
	import Footer from '$lib/components/Footer.svelte';
	import '$lib/styles/global.css';

	const hotspots = [
		{
			number: 1,
			top: '16%',
			left: '22%',
			glowDuration: '2.7s',
			glowDelay: '-1.4s',
			title: 'Master Track',
			description: 'Controls pattern length and plays back deterministically. Child track positions can be altered in real time.'
		},
		{
			number: 2,
			top: '28%',
			left: '7%',
			glowDuration: '3.4s',
			glowDelay: '-0.2s',
			title: '12 Child Tracks',
			description: 'Independent monophonic tracks with 3 FX slots per step for creative sequencing.'
		},
		{
			number: 3,
			top: '82%',
			left: '49%',
			glowDuration: '2.9s',
			glowDelay: '-2.1s',
			title: 'Realtime FX',
			description: 'Distortion, sample-rate reduction, multimode biquad filter, and EQ per track with 4 modulation slots.'
		},
		{
			number: 4,
			top: '82%',
			left: '32%',
			glowDuration: '3.1s',
			glowDelay: '-0.9s',
			title: 'Sample Playback',
			description: 'Advanced playback with looping, multiple play modes, start and end controls, and more.'
		},
		{
			number: 5,
			top: '85%',
			left: '15%',
			glowDuration: '2.5s',
			glowDelay: '-1.8s',
			title: 'Arpeggiation & Randomization',
			description: 'Complex arpeggios with probability per step. Deep randomization options for sequencing FX.'
		},
		{
			number: 6,
			top: '82%',
			left: '1.5%',
			glowDuration: '3.7s',
			glowDelay: '-2.9s',
			title: 'Parameter States',
			description: 'Create a new state and adjust FX parameters. Switch back to a previous state to restore. Sequence state changes on the fly.'
		},
		{
			number: 7,
			top: '5%',
			left: '81%',
			glowDuration: '2.8s',
			glowDelay: '-0.6s',
			title: 'Independent Track Length, Speed, Position',
			description: 'The master track controls pattern length and plays back deterministically. Playhead positions for the 12 child tracks can be altered in real time with various sequencing FX.'
		},
		{
			number: 8,
			top: '6%',
			left: '8%',
			glowDuration: '3.0s',
			glowDelay: '-2.4s',
			title: 'MIDI Out',
			description: 'Fully fledged MIDI out with clock synchronisation for external devices and software.'
		},
		{
			number: 9,
			top: '48%',
			left: '78%',
			glowDuration: '3.3s',
			glowDelay: '-1.1s',
			title: 'Modulation & Parameter Locks',
			description: 'All parameters can be modulated and sequenced in real time.'
		}
	];
</script>

<svelte:head>
	<title>IDIMide - An Experimental Tracker</title>
	<meta name="description" content="IDIMide - An experimental tracker with 12 monophonic tracks, realtime FX, and deep sequencing capabilities. Coming soon." />
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
	<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet" />
</svelte:head>

<div class="grain-overlay"></div>

<Hero />

<main>
	<Screenshot {hotspots} />
	<Subscribe />
</main>

<Footer />
