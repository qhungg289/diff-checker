<script lang="ts">
	import { EditorView, basicSetup } from 'codemirror';
	import { createEventDispatcher, onMount } from 'svelte';

	export let value: string;

	let editor: HTMLDivElement;
	let view: EditorView;

	const dispatch = createEventDispatcher();

	const updateListener = EditorView.updateListener.of((v) => {
		if (v.docChanged) {
			dispatch('update', v.state.doc.toString());
		}
	});

	onMount(() => {
		if (editor) {
			view = new EditorView({
				doc: value,
				extensions: [basicSetup, updateListener],
				parent: editor
			});
		}
	});

	$: {
		if (view) {
			view.dispatch({
				changes: { from: 0, to: view.state.doc.length, insert: value }
			});
		}
	}
</script>

<div bind:this={editor} />
