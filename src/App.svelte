<script>
	import {words} from './Words.svelte'
	let value = location.search.replace('?','');
	let letters, length; $: {
		letters = Array(5).fill('').map((v,i)=>value[i]||' ')
		length = letters.filter(e => e !== ' ').length
	}
	let spoilerTime = 30, spoilerTimeParsed;
	$: spoilerTimeParsed = parseInt(spoilerTime) !== parseInt(spoilerTime) ? 30 : parseInt(spoilerTime)
	let oneDay = 1000 * 3600 * 24
	let wordleStart = 1624057200000
	let numToDate = n => new Date(wordleStart + n*oneDay) 
	let result = {found: false}
	let getWordleInfo = (spoilerTimeParsed, value) => {
		let max = new Date(+new Date() + spoilerTimeParsed * oneDay)
		let min = new Date(+new Date - ((+new Date + 1000 * 3600 ) % oneDay))
		let wordle = words.indexOf(value.toLowerCase())
		console.log(min,numToDate(wordle),max)
		if (wordle === -1 || (+numToDate(wordle) >= +min) && (+numToDate(wordle) <= +max)) return result = {found: false}
		result = {found: true, wordle, date: numToDate(wordle).toDateString()}
	}; 
	$: getWordleInfo(spoilerTimeParsed, value)
</script>
<style>
	.spoilertime {
		width: 4em;
		margin-bottom: 0px;
	}
	.hidden {
		position: absolute;
		top: -9999px; /* what the fuck */
	}
	.letter {
		width: 5rem;
		height: 5rem;
		margin-left: 10px;
		display: inline-flex;
		justify-content: center;
		align-items: center;
		font-size: 4rem;
		line-height: 4rem;
		font-weight: bold;
		vertical-align: middle;
		box-sizing: border-box;
		background-color: #538d4e;
		text-transform: uppercase;
		user-select: none;
	}
	.label {
		text-align: center;
	}
	label > div {
		transition: box-shadow 200ms ease-in-out;
	}
	input:focus + label > div.selected {
		box-shadow: #0007 3px 3px 10px;
	}
	.result {
		font-size: 200%;
		text-align: center;
	}
</style>
<h1 class=result>Is it in Wordle?</h1>
<div>
	<input class=hidden id=input maxlength=5 bind:value>
	<label for=input class=label>
		{#each letters as letter, i}
		   <div class=letter class:selected={i===length}>{letter}</div>
		{/each}
	</label>
</div>
<div class=result>
	{#if length < 5}
    Type a word...			
	{:else}
		{#if result.found}
			That word, Wordle {result.wordle}, is scheduled for {result.date}.	
		{:else}
			That word is not in Wordle :(
		{/if}
	{/if}
</div>
<hr>
<div>
	Note: This app does NOT show words that will be the daily word in less than <input class=spoilertime type=number min=0 step=1 bind:value={spoilerTime}> days. If it is within those <input class=spoilertime type=number min=0 step=1 bind:value={spoilerTime}> days, it will show you that the word is not in Wordle, to prevent spoilers.
</div>
<sub><a href="https://github.com/lxhom/is-it-in-wordle">Source code</a></sub>