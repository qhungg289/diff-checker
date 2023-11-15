<script lang="ts">
	import { EditorState } from '@codemirror/state';
	import { EditorView, basicSetup } from 'codemirror';
	import { createEventDispatcher, onMount } from 'svelte';

	export let value = '';
	export let readOnly = false;
	export let autoHeight = false;

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
				extensions: [
					basicSetup,
					updateListener,
					EditorView.lineWrapping,
					EditorState.readOnly.of(readOnly)
				],
				parent: editor
			});
		}
	});

	$: if (view) {
		view.dispatch({
			changes: { from: 0, to: view.state.doc.length, insert: value }
		});
	}
</script>

<div
	class="basis-1/2 bg-white"
	style="--editor-height: {autoHeight ? 'fit-content' : '300px'};"
	bind:this={editor}
/>

<style lang="postcss">
	:global(.cm-editor .cm-activeLine),
	:global(.cm-editor .cm-gutters),
	:global(.cm-editor .cm-activeLineGutter) {
		background-color: #ffffff44;
		border: none;
	}

	:global(.cm-editor.cm-focused) {
		outline: none;
		box-shadow: var(--tw-ring-inset) 0 0 0 calc(1px + var(--tw-ring-offset-width))
			theme(colors.teal.500);
		border: 1px solid theme(colors.emerald.500);
	}

	:global(.cm-scroller) {
		overflow: auto;
	}

	:global(.cm-editor) {
		height: var(--editor-height);
		border-radius: 0.375rem;
		border: 1px solid theme(colors.gray.200);
		box-shadow: 0 1px 2px 0 rgb(0 0 0 / 0.05);
	}
</style>
