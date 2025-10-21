<script>
	import { onDestroy } from 'svelte';
	import { goto } from '$app/navigation';

	let transitionType = $state('cleanup');
	let duration = $state(5);
	let remainingSeconds = $state(0);
	let isRunning = $state(false);
	let hasCompleted = $state(false);
	let intervalId = null;
	let selectedSong = $state('cleanup');
	let audioElement = $state(null);
	let musicEnabled = $state(true);

	const transitions = {
		cleanup: {
			name: 'Clean Up Time',
			icon: '🧹',
			color: '#e74c3c',
			defaultDuration: 5,
			audioFile: '/songs/Clean-Up-Song.mp3'
		},
		getready: {
			name: 'Get Ready Time',
			icon: '👕',
			color: '#3498db',
			defaultDuration: 10,
			audioFile: '/songs/Getting-Ready-Song.mp3'
		},
		leaving: {
			name: 'Time to Leave',
			icon: '🚗',
			color: '#f39c12',
			defaultDuration: 5,
			audioFile: '/songs/Time-to-Go.mp3'
		},
		bedtime: {
			name: 'Bedtime Routine',
			icon: '🌙',
			color: '#9b59b6',
			defaultDuration: 10,
			audioFile: '/songs/Time-to-Go-to-Sleep.mp3'
		},
		screen: {
			name: 'Screen Time Ending',
			icon: '📱',
			color: '#27ae60',
			defaultDuration: 5,
			audioFile: '/songs/All-Done-Song.mp3'
		}
	};

	$effect(() => {
		if (isRunning && remainingSeconds > 0) {
			intervalId = setInterval(() => {
				remainingSeconds--;
				if (remainingSeconds <= 0) {
					stopTimer();
					hasCompleted = true;
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
		if (remainingSeconds === 0) {
			remainingSeconds = duration * 60;
		}
		hasCompleted = false;
		isRunning = true;
		if (musicEnabled && audioElement) {
			audioElement.play().catch(err => console.error('Audio play failed:', err));
		}
	}

	function pauseTimer() {
		isRunning = false;
		if (audioElement) {
			audioElement.pause();
		}
	}

	function stopTimer() {
		isRunning = false;
		remainingSeconds = 0;
		hasCompleted = false;
		if (audioElement) {
			audioElement.pause();
			audioElement.currentTime = 0;
		}
	}

	function resetTimer() {
		stopTimer();
		remainingSeconds = duration * 60;
		hasCompleted = false;
	}

	function getTimerColor() {
		const percentRemaining = remainingSeconds / (duration * 60);
		if (percentRemaining > 0.5) return transitions[transitionType].color;
		if (percentRemaining > 0.25) return '#f39c12';
		return '#e74c3c';
	}

	function formatTime(seconds) {
		const mins = Math.floor(seconds / 60);
		const secs = seconds % 60;
		return `${mins}:${secs.toString().padStart(2, '0')}`;
	}

	function changeTransition(type) {
		stopTimer();
		transitionType = type;
		duration = transitions[type].defaultDuration;
		hasCompleted = false;
		if (audioElement) {
			audioElement.load();
		}
	}

	onDestroy(() => {
		if (intervalId) clearInterval(intervalId);
	});
</script>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>Transition Timer</h1>
		<div style="width: 100px;"></div>
	</header>

	<div class="intro">
		<p>Musical timers to help with transitions and endings. Make cleanup and getting ready fun!</p>
	</div>

	<div class="transition-selector">
		<h2>Choose Transition Type:</h2>
		<div class="transitions-grid">
			{#each Object.entries(transitions) as [key, trans]}
				<button
					class="transition-card"
					class:active={transitionType === key}
					style="border-color: {trans.color}"
					onclick={() => changeTransition(key)}
				>
					<div class="trans-icon">{trans.icon}</div>
					<div class="trans-name">{trans.name}</div>
				</button>
			{/each}
		</div>
	</div>

	<div class="timer-settings">
		<div class="setting">
			<label for="duration-slider">Duration: {duration} minutes</label>
			<input
				id="duration-slider"
				type="range"
				min="1"
				max="15"
				bind:value={duration}
				onchange={resetTimer}
				disabled={isRunning}
			/>
		</div>

		<div class="setting">
			<label class="checkbox-label">
				<input
					type="checkbox"
					bind:checked={musicEnabled}
				/>
				Play music with timer
			</label>
		</div>
	</div>

	<audio
		bind:this={audioElement}
		src={transitions[transitionType].audioFile}
		loop
	></audio>

	<div class="timer-display" style="border-color: {getTimerColor()}">
		<div class="timer-visual">
			<svg viewBox="0 0 200 200" class="timer-circle">
				<!-- Background circle -->
				<circle
					cx="100"
					cy="100"
					r="80"
					fill="none"
					stroke="#e0e0e0"
					stroke-width="20"
				/>

				<!-- Progress circle -->
				<circle
					cx="100"
					cy="100"
					r="80"
					fill="none"
					stroke={getTimerColor()}
					stroke-width="20"
					stroke-dasharray={2 * Math.PI * 80}
					stroke-dashoffset={
						remainingSeconds > 0
							? 2 * Math.PI * 80 * (1 - remainingSeconds / (duration * 60))
							: 0
					}
					transform="rotate(-90 100 100)"
					style="transition: stroke 0.3s ease;"
				/>
			</svg>

			<div class="timer-text" style="color: {getTimerColor()}">
				{#if remainingSeconds > 0}
					{formatTime(remainingSeconds)}
				{:else}
					{duration}:00
				{/if}
			</div>
		</div>

		<div class="timer-controls">
			{#if !isRunning && remainingSeconds === 0}
				<button class="control-button start" onclick={startTimer}>Start Timer</button>
			{:else if !isRunning && remainingSeconds > 0}
				<button class="control-button start" onclick={startTimer}>Resume</button>
				<button class="control-button reset" onclick={resetTimer}>Reset</button>
			{:else}
				<button class="control-button pause" onclick={pauseTimer}>Pause</button>
				<button class="control-button stop" onclick={stopTimer}>Stop</button>
			{/if}
		</div>
	</div>

	{#if hasCompleted}
		<div class="completion-message" style="background: {transitions[transitionType].color}">
			<h2>🎉 Time's Up! 🎉</h2>
			<p>Great job with {transitions[transitionType].name.toLowerCase()}!</p>
		</div>
	{/if}

	<div class="tips-box">
		<h3>💡 How to Use Transition Timers:</h3>
		<ul>
			<li><strong>Give warnings:</strong> "Timer starts in 1 minute!"</li>
			<li><strong>Sing along:</strong> Make it fun by singing together</li>
			<li><strong>Be consistent:</strong> Use the same timer for the same transitions</li>
			<li><strong>Keep it positive:</strong> "Let's race the timer!" not "Hurry up!"</li>
			<li><strong>Celebrate:</strong> Praise effort when timer ends, even if not quite done</li>
			<li><strong>Visual cues:</strong> Let them watch the circle shrink</li>
		</ul>
	</div>

	<div class="tips-box">
		<h3>🎶 Why Musical Transitions Work:</h3>
		<ul>
			<li>Music makes tasks more enjoyable and engaging</li>
			<li>Songs provide structure and predictability</li>
			<li>Rhythm helps kids stay on pace</li>
			<li>Reduces power struggles over endings</li>
			<li>Creates positive associations with transitions</li>
			<li>Auditory cue helps kids who don't watch timers</li>
		</ul>
	</div>
</div>

<style>
	.container {
		max-width: 900px;
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

	.intro {
		text-align: center;
		font-size: 1.2rem;
		color: #2c3e50;
		margin-bottom: 2rem;
		line-height: 1.6;
	}

	.transition-selector {
		background: white;
		padding: 1.5rem;
		border-radius: 12px;
		margin-bottom: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.transition-selector h2 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 1rem;
	}

	.transitions-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
		gap: 1rem;
	}

	.transition-card {
		background: white;
		border: 3px solid;
		border-radius: 12px;
		padding: 1.5rem;
		cursor: pointer;
		transition: all 0.3s;
		text-align: center;
	}

	.transition-card:hover {
		transform: translateY(-2px);
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
	}

	.transition-card.active {
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
	}

	.trans-icon {
		font-size: 3rem;
		margin-bottom: 0.5rem;
	}

	.trans-name {
		color: #2c3e50;
		font-weight: 600;
		font-size: 0.95rem;
	}

	.timer-settings {
		background: white;
		padding: 1.5rem;
		border-radius: 12px;
		margin-bottom: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.setting {
		margin-bottom: 1.5rem;
	}

	.setting:last-child {
		margin-bottom: 0;
	}

	.setting label {
		display: block;
		font-weight: 600;
		color: #2c3e50;
		margin-bottom: 0.5rem;
	}

	.checkbox-label {
		display: flex;
		align-items: center;
		gap: 0.5rem;
		cursor: pointer;
		font-weight: 600;
	}

	.checkbox-label input[type='checkbox'] {
		width: 20px;
		height: 20px;
		cursor: pointer;
	}

	input[type='range'] {
		width: 100%;
	}

	input[type='range']:disabled {
		opacity: 0.5;
	}

	.timer-display {
		background: white;
		padding: 2rem;
		border-radius: 12px;
		margin-bottom: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
		border: 3px solid;
	}

	.timer-visual {
		position: relative;
		width: 300px;
		margin: 0 auto 2rem;
	}

	.timer-circle {
		width: 100%;
		height: auto;
	}

	.timer-text {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		font-size: 3rem;
		font-weight: bold;
		font-family: 'Courier New', monospace;
	}

	.timer-controls {
		display: flex;
		gap: 1rem;
		justify-content: center;
		flex-wrap: wrap;
	}

	.control-button {
		padding: 1rem 2rem;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		font-size: 1.1rem;
		font-weight: 600;
		transition: all 0.3s;
		color: white;
	}

	.control-button.start {
		background: #27ae60;
	}

	.control-button.start:hover {
		background: #229954;
		transform: translateY(-2px);
	}

	.control-button.pause {
		background: #f39c12;
	}

	.control-button.pause:hover {
		background: #e67e22;
		transform: translateY(-2px);
	}

	.control-button.reset,
	.control-button.stop {
		background: #95a5a6;
	}

	.control-button.reset:hover,
	.control-button.stop:hover {
		background: #7f8c8d;
		transform: translateY(-2px);
	}

	.completion-message {
		padding: 2rem;
		border-radius: 12px;
		text-align: center;
		color: white;
		margin-bottom: 2rem;
	}

	.completion-message h2 {
		font-size: 2rem;
		margin: 0 0 0.5rem 0;
	}

	.completion-message p {
		font-size: 1.3rem;
		margin: 0;
	}

	.tips-box {
		background: #e8f4f8;
		padding: 1.5rem;
		border-radius: 12px;
		border-left: 4px solid #3498db;
		margin-bottom: 1.5rem;
	}

	.tips-box h3 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 1rem;
	}

	.tips-box ul {
		margin: 0;
		padding-left: 1.5rem;
	}

	.tips-box li {
		color: #2c3e50;
		line-height: 1.8;
		margin-bottom: 0.5rem;
	}

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}

		.timer-visual {
			width: 250px;
		}

		.timer-text {
			font-size: 2.5rem;
		}

		.transitions-grid {
			grid-template-columns: repeat(2, 1fr);
		}

		.timer-controls {
			flex-direction: column;
		}

		.control-button {
			width: 100%;
		}
	}
</style>
