<script lang="ts">
	import Editor from '$lib/components/Editor.svelte';
	import type { Diff } from 'fast-diff';
	import diff from 'fast-diff';

	let originalText = '';
	let changedText = '';

	let originalTextResult = '';
	let changedTextResult = '';
	let diffResult: Diff[] = [];

	let isLoading = false;

	$: console.log(diffResult);

	$: removalCount = diffResult.filter((value) => value[0] == diff.DELETE).length;
	$: additionCount = diffResult.filter((value) => value[0] == diff.INSERT).length;

	function handleOriginalTextUpdate(e: CustomEvent<string>) {
		originalText = e.detail;
	}

	function handleChangedTextUpdate(e: CustomEvent<string>) {
		changedText = e.detail;
	}

	function setDiffResult() {
		if (originalText || changedText) {
			isLoading = true;
			originalTextResult = originalText;
			changedTextResult = changedText;
			diffResult = diff(originalText, changedText);
			isLoading = false;
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
				<div class="font-medium text-rose-600 flex items-center gap-2">
					<div class="bg-rose-100 w-fit rounded-full">
						<svg
							xmlns="http://www.w3.org/2000/svg"
							viewBox="0 0 20 20"
							fill="currentColor"
							class="w-4 h-4"
						>
							<path
								fill-rule="evenodd"
								d="M4 10a.75.75 0 01.75-.75h10.5a.75.75 0 010 1.5H4.75A.75.75 0 014 10z"
								clip-rule="evenodd"
							/>
						</svg>
					</div>
					<span>{removalCount} removal</span>
				</div>
				<Editor
					value={originalTextResult}
					readOnly
					autoHeight
					on:update={handleOriginalTextUpdate}
				/>
			</div>
			<div class="basis-1/2 space-y-2">
				<div class="font-medium text-green-600 flex items-center gap-2">
					<div class="bg-green-100 w-fit rounded-full">
						<svg
							xmlns="http://www.w3.org/2000/svg"
							viewBox="0 0 20 20"
							fill="currentColor"
							class="w-4 h-4"
						>
							<path
								d="M10.75 4.75a.75.75 0 00-1.5 0v4.5h-4.5a.75.75 0 000 1.5h4.5v4.5a.75.75 0 001.5 0v-4.5h4.5a.75.75 0 000-1.5h-4.5v-4.5z"
							/>
						</svg>
					</div>
					<span>{additionCount} addition</span>
				</div>
				<Editor value={changedTextResult} readOnly autoHeight on:update={handleChangedTextUpdate} />
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
		disabled={isLoading}
		on:click={setDiffResult}
		class="px-4 py-2 rounded-md bg-emerald-500 hover:bg-emerald-600 disabled:opacity-75 disabled:hover:bg-emerald-500 disabled:cursor-not-allowed transition-colors text-white font-medium"
	>
		{isLoading ? 'Finding...' : 'Find difference'}
	</button>
</div>
