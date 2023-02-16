<script>
	import { onDestroy } from 'svelte';
	function format(raw) {
		if (raw <= 0) return `00`;
		if (raw <= 9) return `0${raw}`;
		return raw;
	}
	function getHours(seconds) {
		return format(Math.floor(seconds / 60 / 60));
	}
	function getMinutes(seconds) {
		return format(Math.floor(seconds / 60) % 60);
	}
	function getSeconds(seconds) {
		return format(seconds % 60);
	}

	function intervalManager(milliseconds) {
		let interval = 0;
		const startTimer = (callback) => {
			interval = setInterval(callback, milliseconds);

			onDestroy(() => {
				clearInterval(interval);
			});
		};
		const clear = () => clearInterval(interval);

		return { clear, startTimer };
	}

	let timer = 0;
	let seconds = 0;
	let minutes = 0;
	let hours = 0;

	let total = 0;

	function calculate() {
		console.log(hours);
		total = hours * 60 * 60 + minutes * 60 + seconds;

		minutes = minutes % 60;
		seconds = seconds % 60;
	}

	let display = '00:00:00';

	const manager = intervalManager(1000);

	const resetTimer = () => {
		manager.clear();
		timer = 0;
		display = '00:00:00';
	};

	const startTimer = () => {
		manager.clear();
		timer = 0;
		timer = total + timer;
		manager.startTimer(() => {
			timer++;
			display = `${getHours(timer)}:${getMinutes(timer)}:${getSeconds(timer)}`;
		});
	};
	onDestroy(() => {
		manager.clear();
	});
</script>

<h1>Welcome to Matt's little stopwatch</h1>

<div>Stop watch</div>
<section>
	<div class="display">
		<h2>{display}</h2>
	</div>
	<div class="flex">
		<button on:click={startTimer}>Start</button>
		<button on:click={resetTimer}>Reset</button>
		<label for="hours">Hours</label>
		<input name="hours" bind:value={hours} type="number" on:change={calculate} />
		<label for="minutes">Minutes</label>
		<input name="minutes" bind:value={minutes} type="number" on:change={calculate} />
		<label for="seconds">Seconds</label>
		<input name="seconds" bind:value={seconds} on:change={calculate} type="number" min="0" />
	</div>
</section>

<style>
	.display {
		text-align: center;
		background: lightblue;
		border-radius: 30px;
	}
	h2 {
		font-size: 64px;
	}
	section {
		width: 80vw;
		margin: 0 auto;
	}
	.flex {
		justify-content: center;
		flex-direction: column;
		align-content: center;
		align-items: center;
		display: flex;
		gap: 10px;
	}
</style>
