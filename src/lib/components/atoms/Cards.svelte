<script lang="ts">
	import { HttpRegex } from '$lib/utils/regex';

	export let additionalClass: string | undefined = undefined;

	export let href: string | undefined = undefined;
	const isExternalLink = !!href && HttpRegex.test(href);
	export let target: '_self' | '_blank' = isExternalLink ? '_blank' : '_self';
	export let rel = isExternalLink ? 'noopener noreferrer' : undefined;

	function getRandomColor() {
		const letters = '0123456789ABCDEF';
		let color = '#';
		for (let i = 0; i < 6; i++) {
			color += letters[Math.floor(Math.random() * 16)];
		}
		return color;
	}

	function getRandomGradient() {
		const color1 = getRandomColor();
		const color2 = getRandomColor();
		const randomDegree = Math.floor(Math.random() * 360);
		return `linear-gradient(${randomDegree}deg, ${color1}, ${color2})`;
	}

	$: randomGradientBackground = getRandomGradient();

	$: tag = href ? 'a' : 'article';
	$: linkProps = {
		href,
		target,
		rel
	};
</script>

<svelte:element
	this={tag}
	class="card {additionalClass}"
	{...linkProps}
	data-sveltekit-preload-data
	{...$$restProps}
	style="background: {randomGradientBackground};"
>
	<div class="body">
		<div class="content">
			<slot name="content" />
		</div>
	</div>
</svelte:element>

<style lang="scss">
	@import '$lib/scss/breakpoints.scss';

	.card {
		display: flex;
		flex-direction: column;
		align-items: flex-end;
		height: 312px;
		position: relative;
		border-radius: 1.5rem;
		overflow: hidden;
		text-decoration: none;
		color: inherit;
		transition: box-shadow 0.3s ease;

		&:hover {
			background: rgba(0, 0, 0, 0.2);
			box-shadow: 0 0 0 3px rgba(255, 49, 0, 1);
		}
	}

	.body {
		display: flex;
		flex-direction: column;
		justify-content: flex-end;
		padding: 1.5rem;
		background: rgba(0, 0, 0, 0.5);
		color: #fff;
		width: 100%;
		height: 100%;
		z-index: 1;

		&:hover {
			background: rgba(0, 0, 0, 0.1);
		}
	}
</style>
