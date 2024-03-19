<script>
	import { onMount } from 'svelte';
	import Weather from './Weather.svelte';
	import Search from './Search.svelte';
	import { fade } from 'svelte/transition';
	import { options } from '$lib/api.js';
	import { cubicInOut } from 'svelte/easing';
	import Loader from './Loader.svelte';

	let searchHeight;
	let searchInput = '';
	let isLoading = false;
	let weather = null;
	let backgroundImageUrl =
		'https://r4.wallpaperflare.com/wallpaper/132/401/75/painting-clouds-sky-landscape-wallpaper-57bfb2f40fd59f8acf4284a1e191c785.jpg';

	onMount(() => {
		const search = document.getElementById('search');
		const searchStyle = getComputedStyle(search);
		let marginTop = parseFloat(searchStyle.marginTop);
		let marginBottom = parseFloat(searchStyle.marginBottom);
		searchHeight = search.offsetHeight + marginTop + marginBottom;
	});

	async function fetchWeather() {
		isLoading = true;
		try {
			const isZipCode = /^\d+$/.test(searchInput); // Check if the input is a zip code
			const apiUrl = isZipCode
				? `https://weatherapi-com.p.rapidapi.com/forecast.json?q=${searchInput}&days=3`
				: `https://weatherapi-com.p.rapidapi.com/forecast.json?q=${searchInput}&days=3`;

			const response = await fetch(apiUrl, options);
			const data = await response.json();
			const { location, current, forecast } = data;
			const { country, name, localtime } = location;
			const { condition, temp_c, feelslike_c, humidity, is_day, pressure_mb, wind_kph } = current;
			const { code, text, icon } = condition;
			const { forecastday } = forecast;
			const [today] = forecastday;
			weather = {
				country,
				name,
				localtime,
				temperature: temp_c,
				feelsLike: feelslike_c,
				humidity,
				isDay: is_day,
				pressure: pressure_mb,
				windSpeed: wind_kph,
				code,
				weatherStatus: text,
				icon,
				today,
			};
			setBackgroundImage(is_day);
		} catch (error) {
			console.error('Error fetching weather data:', error);
		} finally {
			isLoading = false;
		}
	}

	function handleSearch(e) {
		searchInput = e.detail.search;
		fetchWeather();
	}

	function setBackgroundImage(isDay) {
		if (isDay) {
			backgroundImageUrl =
				'https://r4.wallpaperflare.com/wallpaper/880/841/120/mountains-forest-artwork-firewatch-wallpaper-82f06d94b7ca2320baa2a4bd5d75257d.jpg';
		} else {
			backgroundImageUrl =
				'https://r4.wallpaperflare.com/wallpaper/615/294/495/artwork-deer-antlers-forest-wallpaper-3990080df11a5d7b7687880f2011c67d.jpg';
		}
	}
</script>

<!-- ... (the rest of the code remains the same) -->

<main
	transition:fade={{
		duration: 900,
		easing: cubicInOut,
	}}
	style={weather?.isDay === undefined
		? 'background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.3) 0%, rgba(0, 0, 0, 0.3) 100%), url(https://r4.wallpaperflare.com/wallpaper/132/401/75/painting-clouds-sky-landscape-wallpaper-57bfb2f40fd59f8acf4284a1e191c785.jpg)'
		: weather?.isDay === 1
		? 'background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.3) 0%, rgba(0, 0, 0, 0.3) 100%), url(https://r4.wallpaperflare.com/wallpaper/880/841/120/mountains-forest-artwork-firewatch-wallpaper-82f06d94b7ca2320baa2a4bd5d75257d.jpg)'
		: 'background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.3) 0%, rgba(0, 0, 0, 0.3) 100%), url(https://r4.wallpaperflare.com/wallpaper/615/294/495/artwork-deer-antlers-forest-wallpaper-3990080df11a5d7b7687880f2011c67d.jpg)'}
>
	<div class="box">
		<div class="center">
			<div class="stack">
				<Search on:search={handleSearch} />

				{#if isLoading}
					<Loader />
				{:else if weather}
					<Weather {weather} {searchHeight} />
				{/if}
			</div>
		</div>
	</div>
</main>

<style>
	main {
		display: grid;
		grid-template-columns: 1fr;
		min-height: 100svh;
		background: hsl(255, 0%, 25%);
		background-size: cover;
		background-position: center center;
		background-image: linear-gradient(rgba(0, 0, 0, 0.5));
	}

	.box {
		padding-inline: 1rem;
	}

	.center {
		position: relative;
		height: 100%;
		width: min(100%, 65ch);
		margin-inline: auto;
	}

	.stack {
		display: flex;
		flex-direction: column;
		height: inherit;
	}
</style>
