<script lang="ts">
	import type { OutlineObject } from '../../data/interfaces/interfaces';
	import OutlineCardAnimated from '$lib/cards/OutlineCardAnimated.svelte';
	import Toggle from '../../lib/toggle.svelte';
	import allOutlines from '../../data/outlines.json';
	import { outlineMatchesSearchTerm, sortOutlinesAlphabetically } from '../../scripts/helpers';

	let displayedOutlines: OutlineObject[] = allOutlines;
	let alphabetToggleOn = false;
	let searchTerm = null;

	const alphabet = 'abcdefghijklmnopqrstuvwxyz'.split('');

	const alphabetOutlines = allOutlines.filter((outline) =>
		alphabet.some((letter) => outline.letterGroupings && outline.letterGroupings.includes(letter))
	);

	const toggleAlphabetFilter = () => {
		displayedOutlines = alphabetToggleOn ? allOutlines : alphabetOutlines;
		alphabetToggleOn = !alphabetToggleOn;
		searchTerm = null;
	};

	const filterOutlines = (outlines: OutlineObject[], searchTerm: string) => {
		displayedOutlines = outlines.filter((outline) => outlineMatchesSearchTerm(outline, searchTerm));
	};
</script>

<svelte:head>
	<title>Outlines | teeline.online</title>
	<meta
		name="description"
		content="Browse and search an interactive library of hundreds of animated Teeline shorthand outlines, ranging from the alphabet to special abbreviations."
	/>
</svelte:head>

<div class="filters-container">
	<Toggle toggleLabel={`Only show alphabet`} toggleFunction={toggleAlphabetFilter} />
	<input
		class="search-input"
		placeholder="Search for outlines..."
		bind:value={searchTerm}
		on:input={() => {
			const outlinesToFilter = alphabetToggleOn ? alphabetOutlines : allOutlines;
			filterOutlines(outlinesToFilter, searchTerm.trim());
		}}
	/>
</div>

<div class="animated-container">
	{#each sortOutlinesAlphabetically(displayedOutlines) as outlineObject}
		<OutlineCardAnimated {outlineObject} />
	{/each}
</div>

{#if displayedOutlines.length === 0}
	<div class="nothing-found-message">No outlines found matching your search.</div>
{/if}

<style>
	.animated-container {
		display: flex;
		flex-direction: column;
		row-gap: 30px;
	}
	.filters-container {
		margin: 0 auto 2rem auto;
		width: max-content;
	}
	.nothing-found-message {
		margin: 10rem auto;
		text-align: center;
		font-size: 1.8rem;
	}
	@media (min-width: 1025px) {
		.animated-container {
			display: grid;
			column-gap: 30px;
			grid-template-columns: repeat(6, 1fr);
		}
	}
</style>