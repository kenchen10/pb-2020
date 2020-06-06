<svelte:head>
	<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
</svelte:head>
<script>
	import { onMount } from "svelte";

    const apiURL = 'https://raw.githubusercontent.com/2020PB/police-brutality/data_build/all-locations.json';
	let data = [];
	let obj = {};
	let categorizedByState = {};
	let categorizedByStateAndCity = {};
	$:seenCities = [];
	$:isHome = true;
	$:currState = "";

	onMount(async function() {
        const response = await fetch(apiURL);
		obj = await response.json();
		data = obj.data;
		for (let i = 0; i < data.length; i++) {
			let item = data[i];
			let state = item.state;
			let keys = Object.keys(categorizedByState);
			if (keys.includes(state)) {
				categorizedByState[state] = [...categorizedByState[state], item]; 
			} else {
				categorizedByState[state] = [item];
			}
		}
		console.log(categorizedByState)
		const id = 'twitter-wjs'

        // if script was already set, don't load it again.
        if (document.getElementById(id)) return

        var s = document.createElement('script')
        s.id = id
        s.type = 'text/javascript'
        s.async = true
        s.src = '//platform.twitter.com/widgets.js'
        document.getElementsByTagName('head')[0].appendChild(s)
	});

	function handleClick(state) {
		return () => {
			seenCities = [];
			isHome = false;
			currState = state;
		}
	}

	function updateSeenCities(city) {
		if (seenCities.includes(city)) {
			return false;
		}
		seenCities = [...seenCities, city];
		return true;
	}

	function embed(link) {
		const site = link.slice(12, 18);
		return site;
	}

</script>

<div class="container">
	<h1>Police Brutality During the 2020 George Floyd Protests</h1>
	<h3>Edit <a href={obj.edit_at} target="_blank"> here</a>.</h3>
	{#each Object.keys(categorizedByState).sort() as state}
			<button on:click={handleClick(state)} class="btn btn-6 btn-6e">{state}</button>
	{/each}
	{#if isHome === false} 
		<br><br>
		<hr>
		<h2>{currState}</h2>
		{#each categorizedByState[currState] as dataPoint}
			{#if updateSeenCities(dataPoint.city)}
				<h3>{dataPoint.city}</h3>
			{/if}
			{#if dataPoint.date && dataPoint.date_text}
				<p class="date">{dataPoint.date + ": " + dataPoint.date_text}</p>
			{:else if dataPoint.date && !dataPoint.date_text}
				<p class="date">{dataPoint.date}</p>
			{:else if !dataPoint.date && dataPoint.date_text}
				<p class="date">{dataPoint.date_text}</p>
			{/if}
			<p class="name">{dataPoint.name}</p>
			{#each dataPoint.links as link} 
				<a href={link} target="_blank">{link}</a><br>
			{/each}
			<!-- {#if embed(dataPoint.links[0]) === 'reddit'} -->
				<!-- <blockquote class="reddit-card" data-card-created="1591095929"><a href={dataPoint.links[0]}>asdf</a></blockquote>
				<script async src="//embed.redditmedia.com/widgets/platform.js" charset="UTF-8"></script> -->
			<!-- {:else}
				<div>{dataPoint.links[0]}</div>
			{/if} -->
		{/each}
		
	{/if}
</div>

<style>
.date {
	font-style: italic;
}

.container {
	width: 60%;
	margin: auto;
	margin-bottom: 50px;
}

.btn {
	border: none;
	font-family: inherit;
	font-size: inherit;
	color: inherit;
	background: none;
	cursor: pointer;
	padding: 10px;
	display: inline-block;
    margin-right: 6px;
    margin-bottom: 6px;
	letter-spacing: 1px;
	outline: none;
	position: relative;
	-webkit-transition: all 0.3s;
	-moz-transition: all 0.3s;
	transition: all 0.3s;
}

.btn:after {
	content: '';
	position: absolute;
	z-index: -1;
	-webkit-transition: all 0.3s;
	-moz-transition: all 0.3s;
	transition: all 0.3s;
}

.btn:before {
	font-family: 'icomoon';
	font-style: normal;
	font-weight: normal;
	font-variant: normal;
	text-transform: none;
	line-height: 1;
	position: relative;
	-webkit-font-smoothing: antialiased;
}

.btn-6 {
	color: #fff;
	background: black;
	-webkit-transition: none;
	-moz-transition: none;
	transition: none;
}

.btn-6:active {
	top: 2px;
}

.btn-6e {
	border: 2px dashed black;
	border-radius: 6px;
}

.btn-6e:hover {
	background: transparent;
	color: black;
}

h2 {
	text-decoration: underline;
}

hr {
	border: 1px solid #EEEEEE;
}
</style>