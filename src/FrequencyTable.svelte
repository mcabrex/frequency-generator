<script>
</script>

<div class="table-container">
	<table class="frequency-table">
		<thead>
			<tr>
				<th>Octave</th>
				<th>Note</th>
				<th>Frequency (Hz)</th>
			</tr>
		</thead>
		<tbody>
			<slot></slot>
		</tbody>
	</table>
</div>

<style>
	.table-container {
		padding-bottom: 5vh;
	}
	.frequency-table {
		margin: 0 auto;
		border-collapse: collapse;
    	box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
	}
	.frequency-table :global(thead) :global(tr) {
		background-color: rgb(209, 14, 14);
    	color: #ffffff;
    	text-align: left;
	}

	.frequency-table th,
	.frequency-table tr {
	    padding: 12px 15px;
	}

	.frequency-table :global(tbody) :global(tr) {
    	border-bottom: 1px solid #dddddd;
	}
	.frequency-table :global(tbody) :global(tr:nth-of-type(even)) {
		background-color: #f3f3f3;
	}
	.frequency-table :global(tbody) :global(tr:last-of-type) {
		border-bottom: 2px solid rgb(209, 14, 14);
	}
	.frequency-table :global(tr) :global(td) {
    	padding: 12px 15px;
		text-align: left;
	}
</style>