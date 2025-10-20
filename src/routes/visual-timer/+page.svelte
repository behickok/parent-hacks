<script>
	import { onMount, onDestroy } from 'svelte';
	import { goto } from '$app/navigation';

	let totalMinutes = $state(5);
	let remainingSeconds = $state(0);
	let isRunning = $state(false);
	let hasCompleted = $state(false);
	let intervalId = null;
	let inputMinutes = $state(5);

	$effect(() => {
		if (isRunning && remainingSeconds > 0) {
			intervalId = setInterval(() => {
				remainingSeconds--;
				if (remainingSeconds <= 0) {
					stopTimer();
					hasCompleted = true;
					playCompletionSound();
				}
			}, 1000);
		} else if (intervalId) {
			clearInterval(intervalId);
		}

		return () => {
			if (intervalId) clearInterval(intervalId);
		};
	});

	function startTimer() {
		if (!isRunning) {
			if (remainingSeconds === 0) {
				remainingSeconds = totalMinutes * 60;
			}
			hasCompleted = false;
			isRunning = true;
		}
	}

	function pauseTimer() {
		isRunning = false;
	}

	function stopTimer() {
		isRunning = false;
		remainingSeconds = 0;
		hasCompleted = false;
	}

	function resetTimer() {
		stopTimer();
		remainingSeconds = totalMinutes * 60;
		hasCompleted = false;
	}

	function setTimer() {
		totalMinutes = Math.max(1, Math.min(60, inputMinutes));
		remainingSeconds = totalMinutes * 60;
		isRunning = false;
		hasCompleted = false;
	}

	function playCompletionSound() {
		// Visual indication instead of sound
		document.body.style.animation = 'flash 0.5s ease 3';
		setTimeout(() => {
			document.body.style.animation = '';
		}, 1500);
	}

	function getTimerColor() {
		const percentRemaining = remainingSeconds / (totalMinutes * 60);
		if (percentRemaining > 0.5) return '#27ae60'; // Green
		if (percentRemaining > 0.25) return '#f1c40f'; // Yellow
		return '#e74c3c'; // Red
	}

	function getRotation() {
		const percentComplete = 1 - remainingSeconds / (totalMinutes * 60);
		return percentComplete * 360;
	}

	function formatTime(seconds) {
		const mins = Math.floor(seconds / 60);
		const secs = seconds % 60;
		return `${mins}:${secs.toString().padStart(2, '0')}`;
	}

	onDestroy(() => {
		if (intervalId) clearInterval(intervalId);
	});
</script>

<svelte:head>
	<style>
		@keyframes flash {
			0%,
			100% {
				background-color: white;
			}
			50% {
				background-color: #f1c40f;
			}
		}
	</style>
</svelte:head>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>Visual Timer</h1>
		<div style="width: 100px;"></div>
	</header>

	<div class="timer-container">
		{#if !isRunning && remainingSeconds === 0}
			<div class="setup-panel">
				<h2>Set Timer</h2>
				<div class="time-input">
					<input
						type="number"
						bind:value={inputMinutes}
						min="1"
						max="60"
						placeholder="Minutes"
					/>
					<span>minutes</span>
				</div>
				<button class="set-button" onclick={setTimer}>Set Timer</button>

				<div class="quick-buttons">
					<button onclick={() => { inputMinutes = 2; setTimer(); }}>2 min</button>
					<button onclick={() => { inputMinutes = 5; setTimer(); }}>5 min</button>
					<button onclick={() => { inputMinutes = 10; setTimer(); }}>10 min</button>
					<button onclick={() => { inputMinutes = 15; setTimer(); }}>15 min</button>
				</div>
			</div>
		{:else}
			<div class="timer-display">
				<svg class="timer-circle" viewBox="0 0 200 200">
					<!-- Background circle -->
					<circle
						cx="100"
						cy="100"
						r="90"
						fill="none"
						stroke="#e0e0e0"
						stroke-width="20"
					/>

					<!-- Progress circle -->
					<circle
						cx="100"
						cy="100"
						r="90"
						fill="none"
						stroke={getTimerColor()}
						stroke-width="20"
						stroke-dasharray={2 * Math.PI * 90}
						stroke-dashoffset={2 * Math.PI * 90 * (remainingSeconds / (totalMinutes * 60))}
						transform="rotate(-90 100 100)"
						style="transition: stroke-dashoffset 1s linear, stroke 0.3s ease;"
					/>

					<!-- Clock hand -->
					<line
						x1="100"
						y1="100"
						x2="100"
						y2="30"
						stroke="#2c3e50"
						stroke-width="4"
						stroke-linecap="round"
						transform="rotate({getRotation()} 100 100)"
						style="transition: transform 1s linear;"
					/>

					<!-- Center dot -->
					<circle cx="100" cy="100" r="8" fill="#2c3e50" />
				</svg>

				<div class="time-display" style="color: {getTimerColor()}">
					{formatTime(remainingSeconds)}
				</div>

				<div class="color-legend">
					<div class="legend-item">
						<div class="color-box" style="background: #27ae60;"></div>
						<span>Lots of time</span>
					</div>
					<div class="legend-item">
						<div class="color-box" style="background: #f1c40f;"></div>
						<span>Getting close</span>
					</div>
					<div class="legend-item">
						<div class="color-box" style="background: #e74c3c;"></div>
						<span>Almost done</span>
					</div>
				</div>
			</div>

			<div class="controls">
				{#if !isRunning}
					<button class="start-button" onclick={startTimer}>Start</button>
				{:else}
					<button class="pause-button" onclick={pauseTimer}>Pause</button>
				{/if}
				<button class="reset-button" onclick={resetTimer}>Reset</button>
				<button class="stop-button" onclick={stopTimer}>New Timer</button>
			</div>
		{/if}

		{#if hasCompleted}
			<div class="completion-message">
				<h2>Time's Up!</h2>
				<p>Great job!</p>
			</div>
		{/if}
	</div>
</div>

<style>
	.container {
		max-width: 800px;
		margin: 0 auto;
		padding: 2rem;
		min-height: 100vh;
	}

	header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 2rem;
	}

	header h1 {
		font-size: 2rem;
		color: #2c3e50;
		flex: 1;
		text-align: center;
	}

	.back-button {
		background: #3498db;
		color: white;
		border: none;
		padding: 0.75rem 1.5rem;
		border-radius: 8px;
		cursor: pointer;
		font-size: 1rem;
		transition: background 0.3s;
	}

	.back-button:hover {
		background: #2980b9;
	}

	.timer-container {
		background: white;
		border-radius: 12px;
		padding: 3rem;
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
		text-align: center;
	}

	.setup-panel h2 {
		color: #2c3e50;
		margin-bottom: 2rem;
	}

	.time-input {
		display: flex;
		align-items: center;
		justify-content: center;
		gap: 1rem;
		margin-bottom: 2rem;
	}

	.time-input input {
		padding: 1rem;
		font-size: 2rem;
		border: 2px solid #e0e0e0;
		border-radius: 8px;
		width: 150px;
		text-align: center;
	}

	.time-input input:focus {
		outline: none;
		border-color: #3498db;
	}

	.time-input span {
		font-size: 1.5rem;
		color: #7f8c8d;
	}

	.set-button {
		padding: 1rem 3rem;
		font-size: 1.3rem;
		background: #3498db;
		color: white;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		transition: all 0.3s;
		font-weight: 600;
		margin-bottom: 2rem;
	}

	.set-button:hover {
		background: #2980b9;
		transform: translateY(-2px);
	}

	.quick-buttons {
		display: flex;
		gap: 1rem;
		justify-content: center;
		flex-wrap: wrap;
	}

	.quick-buttons button {
		padding: 0.75rem 1.5rem;
		font-size: 1rem;
		background: #ecf0f1;
		color: #2c3e50;
		border: 2px solid #bdc3c7;
		border-radius: 8px;
		cursor: pointer;
		transition: all 0.3s;
	}

	.quick-buttons button:hover {
		background: #bdc3c7;
		transform: translateY(-2px);
	}

	.timer-display {
		position: relative;
	}

	.timer-circle {
		width: 100%;
		max-width: 400px;
		height: auto;
		margin: 0 auto 2rem;
	}

	.time-display {
		font-size: 3rem;
		font-weight: bold;
		margin-bottom: 2rem;
		font-family: 'Courier New', monospace;
	}

	.color-legend {
		display: flex;
		justify-content: center;
		gap: 2rem;
		margin-bottom: 2rem;
		flex-wrap: wrap;
	}

	.legend-item {
		display: flex;
		align-items: center;
		gap: 0.5rem;
	}

	.color-box {
		width: 30px;
		height: 30px;
		border-radius: 4px;
		border: 2px solid #2c3e50;
	}

	.legend-item span {
		color: #2c3e50;
		font-size: 0.9rem;
	}

	.controls {
		display: flex;
		gap: 1rem;
		justify-content: center;
		flex-wrap: wrap;
	}

	.controls button {
		padding: 1rem 2rem;
		font-size: 1.2rem;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		transition: all 0.3s;
		font-weight: 600;
	}

	.start-button {
		background: #27ae60;
		color: white;
	}

	.start-button:hover {
		background: #229954;
		transform: translateY(-2px);
	}

	.pause-button {
		background: #f39c12;
		color: white;
	}

	.pause-button:hover {
		background: #e67e22;
		transform: translateY(-2px);
	}

	.reset-button {
		background: #95a5a6;
		color: white;
	}

	.reset-button:hover {
		background: #7f8c8d;
		transform: translateY(-2px);
	}

	.stop-button {
		background: #e74c3c;
		color: white;
	}

	.stop-button:hover {
		background: #c0392b;
		transform: translateY(-2px);
	}

	.completion-message {
		margin-top: 2rem;
		padding: 2rem;
		background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
		border-radius: 12px;
		color: white;
		animation: celebration 1s ease;
	}

	.completion-message h2 {
		font-size: 2.5rem;
		margin-bottom: 0.5rem;
	}

	.completion-message p {
		font-size: 1.5rem;
	}

	@keyframes celebration {
		0%,
		100% {
			transform: scale(1);
		}
		50% {
			transform: scale(1.05);
		}
	}

	@media (max-width: 600px) {
		.timer-container {
			padding: 1.5rem;
		}

		.controls {
			flex-direction: column;
		}

		.controls button {
			width: 100%;
		}

		.time-display {
			font-size: 2rem;
		}

		.color-legend {
			flex-direction: column;
			gap: 1rem;
		}
	}
</style>
