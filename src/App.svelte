<script lang='typescript'>
	
	import Calendar from './Calendar.svelte'

</script>

<div class="main-container">
	
	<Calendar/>
	<Calendar/>
	<Calendar/>

</div>

<style>

	.main-container {
		width: 500px;
        height: 300px;
        border: 1px solid #ccc;
        border-radius: 5px;
        padding: 2em;
		margin: auto;
		margin-top: 3em;
		font-family: Arial, Helvetica, sans-serif;
	}

</style>
