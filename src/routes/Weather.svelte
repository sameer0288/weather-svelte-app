<script>
	import { fade } from 'svelte/transition';
	export let searchHeight;
	export let weather;

	$: localTime = weather.localtime.split(' ');
	$: weatherDetails = [
		{
			title: 'Feels Like',
			status: `${weather.feelsLike} °C`,
			icon: '&#127777;', // Thermometer icon
		},
		{
			title: 'Wind Speed',
			status: `${weather.windSpeed} km/h`,
			icon: '&#127788;', // Wind icon
		},
		{
			title: 'Humidity',
			status: `${weather.humidity}%`,
			icon: '&#127754;', // Droplet icon
		},
		{
			title: 'Pressure',
			status: `${weather.pressure} hPa`,
			icon: '&#127758;', // Pressure icon
		},
	];

	$: hourlyForecast = weather.today.hour;
	$: hours = [
		{
			icon: hourlyForecast[8].condition.icon,
			condition: hourlyForecast[8].condition.text,
			time: hourlyForecast[8].time.split(' ')[1],
		},
		{
			icon: hourlyForecast[12].condition.icon,
			condition: hourlyForecast[12].condition.text,
			time: hourlyForecast[12].time.split(' ')[1],
		},
		{
			icon: hourlyForecast[16].condition.icon,
			condition: hourlyForecast[16].condition.text,
			time: hourlyForecast[16].time.split(' ')[1],
		},
		{
			icon: hourlyForecast[20].condition.icon,
			condition: hourlyForecast[20].condition.text,
			time: hourlyForecast[20].time.split(' ')[1],
		},
	];

	function getIcon(iconUrl) {
		const regex = /\d+/g;
		const iconCode = iconUrl.match(regex);
		return `https://cdn.weatherapi.com/weather/64x64/day/${iconCode[2]}.png`;
	}
</script>

<section
	transition:fade={{ duration: 900 }}
	class="weather"
	style={`margin: ${searchHeight}px 0 0 0`}
>
	<article class="weather-container">
		<section class="status-stack">
			<h1>
				{weather.name}, {weather.country}
				As of {localTime[1]}
				{localTime[1] > '12' ? 'pm' : 'am'}
			</h1>
			<div>
				<h2>
					<span class="temp">
						{Math.round(weather.temperature)}°C
					</span>
					<br />
					{weather.weatherStatus}
				</h2>
			</div>
		</section>

		<figure>
			<img class="climate-icon" src={getIcon(weather.icon)} alt="Weather status icon" />
		</figure>
	</article>

	<ul class="weather-details">
		{#each weatherDetails as detail}
			<li>
				<span class="icon">{@html detail.icon}</span>
				<h3>{detail.title}</h3>
				<span role="status">{detail.status}</span>
			</li>
		{/each}
	</ul>

	<hr />

	<h2>Hourly Forecast</h2>
	<ul class="hourly-forecast">
		{#each hours as hour}
			<li>
				<article class="hour">
					<img src={getIcon(hour.icon)} alt="Weather icon" />
					<h3>{hour.condition}</h3>
					<p>{hour.time}</p>
				</article>
			</li>
		{/each}
	</ul>
</section>

<style>
	.weather {
		--space: 1rem;
		display: flex;
		flex-direction: column;
		backdrop-filter: blur(0px);
		color: white;
	}

	.weather > * {
		margin-block: 0;
	}
	.weather > * + * {
		margin-top: var(--space);
	}
	.weather-container {
		--space: 1rem;
		display: flex;
		justify-content: space-between;
		gap: var(--space);
	}

	.temp {
		font-size: var(--size-4);
		font-weight: 800;
	}

	figure {
		display: grid;
		place-items: center;
		width: 6rem;
	}

	figure > img {
		aspect-ratio: 1;
		height: 100%;
	}

	@media (max-width: 320px) {
		figure {
			display: none;
		}
	}

	.weather-details {
		--spacing: 10px;
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(min(100%, 150px), 1fr));
		gap: var(--spacing);
		text-align: center;
	}

	.weather-details > li {
		display: grid;
		place-content: center;
		grid-template-rows: auto 1fr auto;
		gap: 0.25rem;
	}

	.icon {
		font-size: 1.5rem;
	}

	@media (min-width: 300px) and (max-width: 760px) {
		.weather-details {
			grid-template-columns: 1fr 1fr;
		}
	}

	.hourly-forecast {
		--spacing: 10px;
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(min(100%, 100px), 1fr));
		gap: var(--spacing);
	}

	@media (min-width: 300px) and (max-width: 760px) {
		.hourly-forecast {
			grid-template-columns: 1fr 1fr;
		}
	}

	article.hour {
		display: grid;
		place-content: center;
		text-align: center;
	}

	article.hour > img {
		justify-self: center;
		width: 3rem;
	}
</style>
