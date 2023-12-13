<script lang="ts">
	import { EditorState, StateEffect, Range, StateField } from '@codemirror/state';
	import { Decoration } from '@codemirror/view';
	import { SearchCursor } from '@codemirror/search';
	import { EditorView, basicSetup } from 'codemirror';
	import { DIFF_DELETE, DIFF_INSERT, type Diff } from 'diff-match-patch';
	import { createEventDispatcher, onMount } from 'svelte';

	export let value = '';
	export let readOnly = false;
	export let autoHeight = false;
	export let diff: Diff[] = [];
	export let diffType: 'INSERT' | 'DELETE' | null = null;

	let editorContainer: HTMLDivElement;
	let view: EditorView;

	const dispatch = createEventDispatcher();

	const updateListener = EditorView.updateListener.of((v) => {
		if (v.docChanged) {
			dispatch('update', v.state.doc.toString());
		}
	});

	const highlightEffect = StateEffect.define<Range<Decoration>[]>();
	const highlightExtension = StateField.define({
		create() {
			return Decoration.none;
		},
		update(value, transaction) {
			value = value.map(transaction.changes);

			for (let effect of transaction.effects) {
				if (effect.is(highlightEffect)) {
					value = value.update({ add: effect.value, sort: true });
				}
			}

			return value;
		},
		provide: (f) => EditorView.decorations.from(f)
	});

	onMount(() => {
		if (editorContainer) {
			view = new EditorView({
				doc: value,
				extensions: [
					basicSetup,
					updateListener,
					EditorView.lineWrapping,
					EditorState.readOnly.of(readOnly),
					highlightExtension
				],
				parent: editorContainer
			});
		}
	});

	$: view?.dispatch({
		changes: {
			from: 0,
			to: view.state.doc.length,
			insert: value
		}
	});

	$: if (diff.length > 0 && view) {
		if (diffType == 'INSERT') {
			let from = 0;
			const diffInsert = diff.filter((d) => d[0] == DIFF_INSERT).map((d) => d[1]);

			const highlightDecoration = Decoration.mark({
				attributes: {
					class: 'bg-green-200'
				}
			});

			diffInsert?.forEach((d) => {
				const cursor = new SearchCursor(view.state.doc, d, from);
				cursor.next();
				if (cursor.value.to) {
					from = cursor.value.to;
					view.dispatch({
						effects: highlightEffect.of([
							highlightDecoration.range(cursor.value.from, cursor.value.to)
						])
					});
				}
			});
		}

		if (diffType == 'DELETE') {
			let from = 0;
			const diffDelete = diff.filter((d) => d[0] == DIFF_DELETE).map((d) => d[1]);

			const highlightDecoration = Decoration.mark({
				attributes: {
					class: 'bg-red-200'
				}
			});

			diffDelete?.forEach((d) => {
				const cursor = new SearchCursor(view.state.doc, d, from);
				cursor.next();
				if (cursor.value.to) {
					from = cursor.value.to;
					view.dispatch({
						effects: highlightEffect.of([
							highlightDecoration.range(cursor.value.from, cursor.value.to)
						])
					});
				}
			});
		}
	}
</script>

<div
	class="basis-1/2 bg-white"
	style="--editor-height: {autoHeight ? 'fit-content' : '300px'};"
	bind:this={editorContainer}
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
