<script lang="ts">
	import Editor from '$lib/Editor.svelte';
	import type { Diff } from 'fast-diff';
	import diff from 'fast-diff';

	let originalText = '';
	let changedText = '';
	let diffResult: Diff[] = [];

	$: console.log(diffResult);

	function handleOriginalTextUpdate(e: CustomEvent<string>) {
		originalText = e.detail;
	}

	function handleChangedTextUpdate(e: CustomEvent<string>) {
		changedText = e.detail;
	}

	function setDiffResult() {
		if (originalText && changedText) {
			diffResult = diff(originalText, changedText);
		}
	}
</script>

<svelte:head>
	<title>Diffchecker</title>
	<meta name="description" content="A simple tool to check the diff between 2 blocks of text." />
</svelte:head>

<div class="flex flex-col items-center gap-6 mb-8">
	{#if diffResult.length}
		<div class="flex gap-4 w-full">
			<div class="basis-1/2 space-y-2">
				<p class="font-medium">Original text</p>
				<Editor value={originalText} readOnly autoHeight on:update={handleOriginalTextUpdate} />
			</div>
			<div class="basis-1/2 space-y-2">
				<p class="font-medium">Changed text</p>
				<Editor value={changedText} readOnly autoHeight on:update={handleChangedTextUpdate} />
			</div>
		</div>
	{/if}

	<div class="flex gap-4 w-full">
		<div class="basis-1/2 space-y-2">
			<p class="font-medium">Original text</p>
			<Editor value={originalText} on:update={handleOriginalTextUpdate} />
		</div>
		<div class="basis-1/2 space-y-2">
			<p class="font-medium">Changed text</p>
			<Editor value={changedText} on:update={handleChangedTextUpdate} />
		</div>
	</div>

	<button
		on:click={setDiffResult}
		class="px-4 py-2 rounded-md bg-emerald-500 hover:bg-emerald-600 transition-colors text-white font-medium"
	>
		Find difference
	</button>
</div>
