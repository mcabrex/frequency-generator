<script>
	import NoteInput from './NoteInput.svelte'
	import FrequencyTable from './FrequencyTable.svelte'
	
	let name = 'world';
	let octave= 0
	let letter= 'C'
	let alter= 0
	let fixedOct = 4
	let fixedLet = 'A'
	let fixedAlt = 0
	let fixedFreq = 440
	let musicalAlphabet=  ['C','D','E','F','G','A','B']
	
	let semitonalCalculator = ({octave,letter,alter}) => {
		let baseScale = 'C-D-EF-G-A-B';
		let octaveTotal = octave * 12
		let letterTotal = baseScale.indexOf(letter)
    	let semitonalTotal = octaveTotal+letterTotal+alter
		return semitonalTotal
	}
	
	let semitonalDistanceCalculator  = (baseNote,fixedNote) => {
		const fixedTotal = semitonalCalculator(fixedNote)
		const baseTotal = semitonalCalculator(baseNote)
		
		return baseTotal - fixedTotal
		//make sure to subtract the fixedTotal from the baseTotal to determine whether or not the baseTotal is lower or higher than the fixedTotal
	}
	
	const noteFrequencyGenerator = (baseNote,fixedNote={
			frequency: 440,
			octave: 4,
			letter: 'A',
			alter: 0
	}) => {
		//in order for the formula to work a fixed note and fixed frequency must first be defined, A4 = 440Hz is a common choice
		//fixedNote already has this set up as a default value
		const semitonalDistance = semitonalDistanceCalculator(baseNote,{...fixedNote})
		const root = Math.pow(2,1/12)
		const frequency = fixedNote.frequency * (Math.pow(root,semitonalDistance))
		return frequency.toFixed(2)
	}
	
	$: baseNote = {octave,letter,alter}
	$: fixedNote = {
		octave: fixedOct,
		letter: fixedLet,
		alter: fixedAlt,
		frequency: fixedFreq
	}

	$: noteFrequency = noteFrequencyGenerator(baseNote,fixedNote)
	
	let noteRows = musicalAlphabet.flatMap((letter,ind,arr) => {
		if(letter === 'E' || letter === 'B') return letter === 'E' ? 'E' : 'B';
		return [letter,`${letter}♯/${arr[ind+1 % arr.length]}♭`]
	});
	let tableOctaves = [0,1,2,3,4,5,6,7,8];
	let currentOctave = 0;
	let currentView = "Generator";
</script>

<h1 class="title">Note Frequency {currentView}</h1>

<div class="button-view">
	<button 
		on:click={()=>currentView = "Tuning"} 
		class:active={currentView === "Tuning"} 
		class="button-general"
	>Tuning
	</button>
	/
	<button 
		on:click={()=>currentView = "Generator"} 
		class:active={currentView === "Generator"} 
		class="button-general"
	>Generator
	</button>
	/
	<button 
		on:click={()=>currentView = "Table"} 
		class:active={currentView === "Table"} 
		class="button-general"
	>Table
	</button>
</div>

{#if currentView === "Generator"}
	<p>Plug in note information below to generate its sound frequency in hertz (Hz)</p>
	<p>The 'Alter' input field is used to describe the chromatic alteration of a note in semitones (e.g., -1 for flat, 1 for sharp)</p>
	
	<NoteInput frequency={noteFrequency}>
		<legend slot="legend" class="input-legend">
			Note
		</legend>

		<input type=number bind:value={octave} min=0 name="octave" slot="octave" class="input-field"/>

		<select bind:value={letter} slot="letter" class="input-field">
			{#each musicalAlphabet as noteLetter}
				<option value={noteLetter}>
					{noteLetter}
				</option>
			{/each}			
		</select>

		<input type=number bind:value={alter} name="alter" slot="alter" class="input-field"/>
	</NoteInput>
{:else if currentView === "Table"}
	<p>Pick an octave below to display all the frequencies of the octave in hertz (Hz)</p>
	<h3 class="table-octaves">Octaves:
	{#each tableOctaves as tableOctave}
		<button 
			on:click={()=>currentOctave=tableOctave} 
			class="button-general" 
			class:active={currentOctave===tableOctave}
		>{tableOctave}
		</button>
	{/each}
	</h3>
	<FrequencyTable>
		{#each noteRows as note}
			<tr>
				<td>{currentOctave}</td>
				<td>{note}</td>
				<td>{
					noteFrequencyGenerator({
						octave: currentOctave,
						letter: note[0],
						alter: note[1] ? 1 : 0
					})
				}</td>
			</tr>
		{/each}
	</FrequencyTable>

{:else if currentView === "Tuning"}
	<p>Plug in note information below to choose the tuning for the note frequencies used in this app</p>
	<p>The 'Alter' input field is used to describe the chromatic alteration of a note in semitones (e.g., -1 for flat, 1 for sharp)</p>
	<p>**Note: By default this program is set to the tuning of A4 at frequency 440Hz, this is the standard tuning used to calibrate acoustic equipment and tune musical instruments</p>

	<NoteInput frequency={noteFrequency}>
		`${'x'+fixedAlt}` : `${'x'+Math.abs(fixedAlt)}`
		<h2 slot="title">Tuning: 
			{fixedLet}<sub>{fixedOct}</sub>{#if fixedAlt > 0}{'♯'}{#if fixedAlt > 1}<sub>{fixedAlt}</sub>{/if}{:else if fixedAlt < 0}♭{#if fixedAlt < -1}<sub>{Math.abs(fixedAlt)}</sub>{/if}{/if}{fixedFreq}
		</h2>

		<legend slot="legend" class="input-legend">
			Tuning
		</legend>

		<input type=number bind:value={fixedOct} min=0 name="octave" slot="octave" class="input-field"/>

		<select bind:value={fixedLet}  slot="letter" class="input-field" >
			{#each musicalAlphabet as noteLetter}
					<option value={noteLetter}>
						{noteLetter}
					</option>
			{/each}			
		</select>

		<input type=number bind:value={fixedAlt} name="alter" slot="alter" class="input-field"/>
	</NoteInput>
{/if}

<style>
	:global(body){
		text-align: center;
	}
	.active {
		color: rgb(209, 14, 14);
	}
	.title {
		margin: 0.25em 0 0;
	}
	.button-general {
		border-style: none;
		background-color: white;
	}
	.button-general:hover {
		color: rgb(209, 14, 14);
	}
	.button-view {
		font-size: 1.75em;
	}
	.button-view button {
		padding: 0;
		margin: 0;
	}
	.table-octaves {
		margin: 0;
	}
	.table-octaves button {
		margin: 0;
	}
	.input-field {
		margin: 0.5em 0;
		border-radius: 2em;
		text-align: center;
		border-color: black;
	}
	.input-legend {
		text-align: center;
		font-weight: bold;
		font-size: 1.5em;
		font-family: Arial;
	}
</style>