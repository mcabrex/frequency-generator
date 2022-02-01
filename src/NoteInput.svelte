<script>
   export let frequency
</script>

<slot name="title">
   <h2 class="frequency">Note Frequency = {frequency}Hz</h2>
</slot>
<fieldset class="note-inputs">

   <slot name="legend">
      <legend class="input-legend">Note Inputs</legend>
   </slot>
   
   <label for="octave" class="input-title">
      Octave
   </label>
   <slot name="octave" class="input-octave"></slot>

   <label for="letter" class="input-title">
      Letter
   </label>
   <slot name="letter"></slot>
   
   <label for="alter" class="input-title">
      Alter
   </label>
   <slot name="alter"></slot>
</fieldset>

<style>
   .frequency {
		color: rgb(209, 14, 14);
      margin-top: 0;
	}
  .note-inputs {
     margin: 0 auto;
     width: fit-content;
      height: fit-content;
      grid-template-columns: [label] 30% [input] 70%;
      grid-template-rows: [octave] 33% [letter] 33% [alter] 33%;
      display: grid;
  }
	.input-title {
		align-self: center;
		margin: 0.5em 0;
	}
	.input-legend {
		text-align: center;
		font-weight: bold;
		font-size: 1.5em;
		font-family: Arial;
	}
</style>