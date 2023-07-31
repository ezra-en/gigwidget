<script lang="ts">
	import { basicSetup, EditorView } from 'codemirror';
	import { EditorState, Compartment, Transaction } from '@codemirror/state';
	import { onMount } from 'svelte';

	let language = new Compartment(),
		tabSize = new Compartment();

	let state = EditorState.create({
		extensions: [basicSetup, tabSize.of(EditorState.tabSize.of(2))],
		doc: `Oceans - Hillsong United
# Capo 7
[Intro]
Em     D/F# G      D      C

[Verse 1]
Em                  D/F#    G
   You call me out upon the waters
           D                  C
The great unknown where feet may fail
Em                      D/F#   G
   And there I find you in the mystery
      D                   C
In oceans deep, my faith will stand

[Chorus]
C            G          D
  And I will call upon Your name
C             G           D
  And keep my eyes above the waves
            C                 G             D
When oceans rise my soul will rest in Your embrace
         C         D    Em      D/F# G     D     C
For I am Yours and You are mine

[Verse 2]
Em                       D/F#    G
   Your grace abounds in deepest waters
           D                 C
Your sovereign hand will be my guide
Em                         D/F#    G
   Where feet may fail and fear surrounds me
          D                         C
You’ve never failed and You won’t start now

[Chorus]
C           G          D
  So I will call upon Your name
C             G           D
  And keep my eyes above the waves
            C                 G             D
When oceans rise my soul will rest in Your embrace
         C         D    Em       D/F# G     D     C
For I am Yours and You are mine, oh
          Em      D/F# G     D     C
And You are mine, oh

[Bridge]
Em     C     G     D     
x2

Em                          C
   Spirit lead me where my trust is without borders
        G                      D
Let me walk upon the water wherever You would call me
Em                         C
   Take me deeper than my feet could ever wander
        G                                  D
And my faith will be made stronger in the presence of my Savior

Em                          C
   Spirit lead me where my trust is without borders
        G                      D
Let me walk upon the water wherever You would call me
Em                         C
   Take me deeper than my feet could ever wander
        G                                  D
And my faith will be made stronger in the presence of my Savior

Em                          C
   Spirit lead me where my trust is without borders
        G                      D
Let me walk upon the water wherever You would call me
Em                         C
   Take me deeper than my feet could ever wander
        G                                  D
And my faith will be made stronger in the presence of my Savior

C                          G
  Spirit lead me where my trust is without borders
        D                      Am
Let me walk upon the water wherever You would call me
C                         G
  Take me deeper than my feet could ever wander
        D                                  Am
And my faith will be made stronger in the presence of my Savior

Em                 D/F#     G
   Spirit lead me where my trust is without borders
        D                      Am
Let me walk upon the water wherever You would call me
Em                 D/F#    G
   Take me deeper than my feet could ever wander
        D                                  Am
And my faith will be made stronger in the presence of my Savior

Em                 D/F#     G
   Spirit lead me where my trust is without borders
        D                      Am
Let me walk upon the water wherever You would call me
Em                     D/F#    G
   And take me deeper than my feet could ever wander
        D                                  Am
And my faith will be made stronger in the presence of my Savior

[Instrumental]
Em     D/F# G      D      Am      
x2

[Chorus]
C        G            D
  I will call upon Your name
C         G           D
  Keep my eyes above the waves
C              G             D
  My soul will rest in Your embrace
     C         D    Em
I am Yours and You are mine

[Outro]
Em  D/F#  G  D  C   Em   G

`
	});
	let editor: any;
	let renderContent: string;
	let doc;
	const parser = new ChordSheetJS.ChordsOverWordsParser();
	let song;
	const formatter = new ChordSheetJS.HtmlTableFormatter();
	let disp: string;
	let view: EditorView;
	onMount(() => {
		view = new EditorView({
			state,
			parent: editor,
			dispatch: (tr) => dispatchTr(tr, view)
		});
		render(view);
	});

	import ChordSheetJS from 'chordsheetjs';

	let transposeDelta = 0;

	function transUp() {
		transposeDelta += 1;
		render(view);
	}
	function transDown() {
		transposeDelta += -1;
		render(view);
	}
	function transReset() {
		transposeDelta = 0;
		render(view);
	}

	function dispatchTr(tr: Transaction, view: EditorView) {
		view.update([tr]);
		render(view);
	}
  let chords

	function render(view: EditorView) {
		doc = view.state.doc.toString();
		song = parser.parse(doc);
		let songTransposed = song.transpose(transposeDelta);
    // chords = song.
		disp = formatter.format(songTransposed);
		renderContent = disp;
	}



</script>

<main class="flex flex-row gap-2 w-screen">
	<div bind:this={editor} class="my-2 ml-2 border shadow" />
	<div class="prose w-1/3 my-2 p-2 border shadow">{@html renderContent}</div>
	<div class="h-fit flex flex-col gap-2 items-center my-2">
		<button class="rounded bg-blue-300 p-2 w-10 font-semibold" on:click={transUp}>+1</button>
		<button class="rounded bg-blue-300 p-2 w-10 font-semibold border border-gray-400 shadow" on:click={transReset}>{transposeDelta}</button>
		<button class="rounded bg-blue-300 p-2 w-10 font-semibold" on:click={transDown}>-1</button>
	</div>
  <!-- <div id="chart" class="">{chords}</div> -->
</main>

<style>
	:global(.chord) {
		font-weight: 600;
	}
	:global(td) {
		padding: 0;
		white-space: pre;
	}
</style>
