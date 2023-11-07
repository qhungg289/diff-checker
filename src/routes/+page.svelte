<script lang="ts">
	import Editor from '$lib/Editor.svelte';

	let originalText = '';
	let changedText = '';

	function handleOriginalTextUpdate(e: CustomEvent<string>) {
		originalText = e.detail;
	}

	function handleChangedTextUpdate(e: CustomEvent<string>) {
		changedText = e.detail;
	}
</script>

<h1>Diff checker</h1>
<Editor value={originalText} on:update={handleOriginalTextUpdate} />
<Editor value={changedText} on:update={handleChangedTextUpdate} />
