<script lang="ts">
	import { goto } from '$app/navigation';
	import ContributorLink from '$lib/components/contributor-link.svelte';
	import type { components } from '$lib/cosearch';

	export let data;

	let sorted_contributors: null | components['schemas']['ContributorShort'][] = null;

	function normalise_contributor_for_sorting(
		contributor: components['schemas']['ContributorShort']
	) {
		if (!contributor.display_name) {
			return contributor.local_handle.toLowerCase();
		}

		const split = contributor.display_name.split(' ');
		return split[split.length - 1].toLowerCase();
	}

	$: {
		if (data.contributors) {
			sorted_contributors = data.contributors.sort((a, b) => {
				if (normalise_contributor_for_sorting(a) < normalise_contributor_for_sorting(b)) {
					return -1;
				}
				if (normalise_contributor_for_sorting(a) > normalise_contributor_for_sorting(b)) {
					return 1;
				}
				return 0;
			});
		}
	}
</script>

<nav class="flex" aria-label="Breadcrumb">
	<ol role="list" class="flex items-center space-x-0">
		<li>
			<div>
				<a href="/contributors" class="text-gray-400 hover:text-gray-500">
					Contributors {#if data.contributors}({data.contributors.length}){/if}
				</a>
			</div>
		</li>
		<li>
			<div class="flex items-center">
				<svg
					class="h-5 w-5 flex-shrink-0 text-gray-400"
					viewBox="0 0 20 20"
					fill="currentColor"
					aria-hidden="true"
				>
					<path
						fill-rule="evenodd"
						d="M7.21 14.77a.75.75 0 01.02-1.06L11.168 10 7.23 6.29a.75.75 0 111.04-1.08l4.5 4.25a.75.75 0 010 1.08l-4.5 4.25a.75.75 0 01-1.06-.02z"
						clip-rule="evenodd"
					/>
				</svg>
			</div>
		</li>
	</ol>
</nav>

<div>
	<button
		type="button"
		class="inline-flex items-center gap-x-1.5 rounded-md bg-indigo-600 px-2.5 py-1.5 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
		on:click={() => goto('/contributors/new')}
	>
		<i class="fa-solid fa-circle-plus mr-1"></i>
		Add contributor
	</button>
</div>

<ul class="list-disc ml-4 marker:text-gray-500">
	{#if sorted_contributors}
		{#each sorted_contributors as contributor}
			<li>
				<div class="flex items-center space-x-1">
					<ContributorLink {contributor} />
				</div>
			</li>
		{/each}
	{/if}
</ul>
