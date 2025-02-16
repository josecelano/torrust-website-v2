<script lang="ts">
	import Tag from '$lib/components/atoms/Tag.svelte';
	import { formatDate } from '$lib/utils/date';
	import ShareButton from '$lib/components/singletons/ShareButton.svelte';

	import { keywords, siteBaseUrl, title } from '$lib/data/meta';
	import type { BlogPost } from '$lib/utils/types';
	import Image from '$lib/components/atoms/Image.svelte';
	import PrevNextPost from '$lib/components/singletons/PrevNextPost.svelte';
	import { allPosts } from '$lib/data/blog-posts';
	import RelatedPosts from '$lib/components/organisms/RelatedPosts.svelte';

	export let data: { post: BlogPost; allPosts: BlogPost[] };
	let post: BlogPost;

	$: ({ post } = data);

	let metaKeywords = keywords;

	$: {
		if (post?.tags?.length) {
			metaKeywords = post.tags.concat(metaKeywords);
		}
		if (post?.keywords?.length) {
			metaKeywords = post.keywords.concat(metaKeywords);
		}
	}
</script>

<svelte:head>
	{#if post}
		<meta name="keywords" content={metaKeywords.join(', ')} />

		<meta name="description" content={post.excerpt} />
		<meta property="og:description" content={post.excerpt} />
		<meta name="twitter:description" content={post.excerpt} />
		<link rel="canonical" href="{siteBaseUrl}/{post.slug}" />

		<title>{post.title} - {title}</title>
		<meta property="og:title" content="{post.title} - {title}" />
		<meta name="twitter:title" content="{post.title} - {title}" />

		{#if post.coverImage}
			<meta property="og:image" content="{siteBaseUrl}{post.coverImage}" />
			<meta name="twitter:image" content="{siteBaseUrl}{post.coverImage}" />
		{/if}
	{/if}
</svelte:head>

<div class="container">
	<main>
		<article id="article-content">
			<div class="header">
				{#if post.tags?.length}
					<div class="tags">
						{#each post.tags as tag}
							<Tag {tag}>
								{tag}
							</Tag>
						{/each}
					</div>
				{/if}
				{#if post}
					<h1>{post.title}</h1>
					<p>{post.excerpt}</p>
					<div class="note">
						<div>
							{#if post.contributor}
								<a class="author" href={'/contributor/' + post.contributorSlug}
									>{post.contributor}</a
								>
								-
							{/if}
							{formatDate(post.date)}
						</div>
						<ShareButton slug={post.slug} title={post.title} />
					</div>
				{/if}
			</div>
			{#if post && post.coverImage}
				<div class="cover-image">
					<Image src={post.coverImage} alt={post.title} />
				</div>
			{/if}
			<div class="content">
				<slot />
			</div>
		</article>

		<PrevNextPost currentPost={post} {allPosts} />

		{#if post.relatedPosts && post.relatedPosts.length > 0}
			<div class="container-related-articles">
				<h2>Related articles</h2>
				<RelatedPosts posts={post.relatedPosts} />
			</div>
		{/if}
	</main>
</div>

<style lang="scss">
	@import '$lib/scss/_mixins.scss';
	@import '$lib/scss/breakpoints.scss';

	.container {
		background: rgba(26, 26, 26, 1);
		color: rgba(245, 245, 245, 0.96);
	}

	#article-content {
		--main-column-width: 65ch;
		position: relative;
		padding-top: 40px;
		padding-bottom: 80px;
		padding-right: 15px;
		padding-left: 15px;

		.cover-image {
			padding-top: 1.5rem;
		}

		@include for-iphone-se {
			padding-left: 0;
			padding-right: 0;
		}

		@include for-tablet-portrait-up {
			padding-right: 20px;
			padding-left: 20px;
		}

		@include for-tablet-landscape-up {
			--main-column-width: 170ch;
			padding-right: 30px;
			padding-left: 30px;
		}

		display: flex;
		flex-direction: column;
		text-align: left;
		gap: 30px;

		.header {
			display: flex;
			flex-direction: column;
			align-items: flex-start;
			justify-content: flex-start;
			text-align: left;
			gap: 10px;
			width: min(var(--main-column-width), 100%);
			margin: 0 auto;

			h1 {
				font-size: 40px;
			}

			p {
				color: rgba(245, 245, 245, 0.8);
				font-size: 20px;
			}

			.note {
				display: flex;
				align-items: center;
				justify-content: space-between;
				width: 100%;
				font-size: 90%;
				color: rgba(245, 245, 245, 0.96);
			}

			.author {
				color: rgba(245, 245, 245, 0.96);
			}

			.author:hover {
				color: rgba(255, 49, 0, 0.96);
			}
		}

		.cover-image {
			width: min(var(--main-column-width), 100%);
			margin: 0 auto;
			max-height: 400px;
			box-shadow: var(--image-shadow);
			border-radius: 6px;
		}

		:global(.cover-image img) {
			max-height: 400px;
			object-fit: cover;
		}

		.content {
			display: grid;
			grid-template-columns:
				1fr
				min(var(--main-column-width), 100%)
				1fr;

			:global(> *) {
				grid-column: 2;
			}

			:global(> .full-bleed) {
				grid-column: 1 / 4;
				width: 100%;
				max-width: 1600px;
				margin-left: auto;
				margin-right: auto;
			}
		}

		.tags {
			display: flex;
			align-items: flex-start;
			justify-content: flex-start;
			flex-wrap: wrap;
		}
	}

	.container-related-articles {
		h2 {
			margin-block: 64px 48px;
			text-align: center;
		}
	}
</style>
