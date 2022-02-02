# Note Frequency Generator

See it live at https://note-frequency.surge.sh/

The Note Frequency Generator is a simple website designed to generate a frequency in hertz for any given note in Western music theory relative to equal temperament.

# Context 📚

In Western music theory the pitch of every note (and therefore the frequency) is defined by three qualities: the note's octave, letter, and alter.

**The Octave** An octave is the distance between one pitch and another with is double it's frequency.

**The Musical Alphabet** The Musical Alphabet "A B C D E F G" defines every note in an octave. 

**The Alter** The Alter represents the chromatic alteration of a note in semitones (e.g., -1 for flat, 1 for sharp)

**Equal Temperament** An equal temperant is a standard for tuning which divides an octave by 12 *equal* steps

# Content

There are three sections to this web app

**Tuning** For this to work you need to choose a fixed note and set a frequency for it, by default the tuning for this app is A<sub>4</sub> at 440Hz which is a pretty common tuning choice for equal temperament. The tuning of everything else in this app will be based on this.

**Note Frequency Generator** The main input for this app, simply plug in note information to get its frequency in Hz 

**Note Frequency Table** A table to display the frequencies of every note in a scale 

# How It Works ⚙️

## Formula

The formula for the frequency of notes in the Equal Tempered Scale is as follows:

> ### *fn = f0 * (a)n*

- **f0** = the frequency of one fixed note which must be defined, generally okay to define the A above middle C (A4) at f0 = 440Hz
- **n** = the number of half steps away from the fixed note you are. If you are at a higher note, n is positive. If you are on a lower note, n is negative.
- **fn** = the frequency of the note n half steps away.
- **a** = (2)1/12 = the twelth root of 2 

## Javascript

The hardest part of turning this function into something useable in Javascript is calculating the amount of semitones in between two distinct notes. To do this I separated this formula into three functions.

**Semitonal Calculator** 

The purpose of this function is to determine how many half-steps (semitones) there are leading up to an individual note. There needed to be a predefined base scale to represent the musical alphabet and the semitones that naturally occur in between them. 

To calculate how many half-steps there are leading up to any individual note I simply multiplied the notes octave by 12 
Added the index of the letter and then added the alter (which will either positively or negatively impact the total)

```
    let semitonalCalculator = ({octave,letter,alter}) => {
        let baseScale = 'C-D-EF-G-A-B';
        let octaveTotal = octave * 12
        let letterTotal = baseScale.indexOf(letter)
        let semitonalTotal = octaveTotal+letterTotal+alter
        return semitonalTotal
    }
```

**Semitonal Distance Calculator**

In order to get the semitonal (half-step) distance between two notes that would be either positive or negative realtive to a fixed note I subtracted the fixed semitonal total from the total of a base note. In order for this to work the the semitonal totals **must** be grounded in the same relative scale. This means that both of these numbers must be based on the base scale you set up in finding the totals in the semitonal calculator above. 

```
	let semitonalDistanceCalculator  = (baseNote,fixedNote) => {
		const fixedTotal = semitonalCalculator(fixedNote)
		const baseTotal = semitonalCalculator(baseNote)
		
		return baseTotal - fixedTotal
	}
```

**Note Frequency Generator**

Now that the semitonal distance is out of the way you can simple plug in the formula, I fixed the frequency to two decimal places for simplicity's sake.

Just to reiterate, by default I set the fixed note to A<sub>4</sub> at 440Hz so you don't need to change the tuning unless you want to.

```
	let noteFrequencyGenerator = (baseNote,fixedNote={
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
```