<script>
	import { onDestroy } from 'svelte';
	import { goto } from '$app/navigation';

	let transitionType = $state('cleanup');
	let duration = $state(5);
	let remainingSeconds = $state(0);
	let isRunning = $state(false);
	let intervalId = null;
	let selectedSong = $state('cleanup');

	const transitions = {
		cleanup: {
			name: 'Clean Up Time',
			icon: '🧹',
			color: '#e74c3c',
			defaultDuration: 5,
			songs: [
				{
					id: 'cleanup',
					name: 'Clean Up Song',
					lyrics: [
						'Clean up, clean up',
						'Everybody, everywhere',
						'Clean up, clean up',
						'Everybody do your share!'
					]
				},
				{
					id: 'cleanup2',
					name: 'Tidy Up Time',
					lyrics: [
						'It\'s time to tidy up our space',
						'Put the toys back in their place',
						'Working together, we can do it',
						'Clean up time, let\'s get to it!'
					]
				}
			]
		},
		getready: {
			name: 'Get Ready Time',
			icon: '👕',
			color: '#3498db',
			defaultDuration: 10,
			songs: [
				{
					id: 'getready',
					name: 'Getting Ready Song',
					lyrics: [
						'Time to get ready, here we go',
						'Brush our teeth and put on clothes',
						'Pack our bag and tie our shoes',
						'We\'re getting ready, no time to lose!'
					]
				},
				{
					id: 'morning',
					name: 'Morning Routine',
					lyrics: [
						'This is the way we start our day',
						'Start our day, start our day',
						'This is the way we start our day',
						'So early in the morning!'
					]
				}
			]
		},
		leaving: {
			name: 'Time to Leave',
			icon: '🚗',
			color: '#f39c12',
			defaultDuration: 5,
			songs: [
				{
					id: 'leaving',
					name: 'Time to Go',
					lyrics: [
						'It\'s time to go, it\'s time to go',
						'Get your things and off we go',
						'Say goodbye and out the door',
						'Time to leave, we can\'t wait more!'
					]
				},
				{
					id: 'goodbye',
					name: 'Goodbye Song',
					lyrics: [
						'Goodbye, goodbye',
						'We\'ll see you soon',
						'Goodbye, goodbye',
						'We\'ll be back at noon! (or afternoon!)'
					]
				}
			]
		},
		bedtime: {
			name: 'Bedtime Routine',
			icon: '🌙',
			color: '#9b59b6',
			defaultDuration: 10,
			songs: [
				{
					id: 'bedtime',
					name: 'Bedtime Song',
					lyrics: [
						'Now it\'s time to go to sleep',
						'Close your eyes and count the sheep',
						'Tomorrow brings another day',
						'For now, it\'s time to rest and play in dreams!'
					]
				},
				{
					id: 'lullaby',
					name: 'Gentle Lullaby',
					lyrics: [
						'Time for bed, my sleepy head',
						'Snuggle in and rest instead',
						'Dream of things both big and small',
						'Goodnight, sleep tight, I love you all!'
					]
				}
			]
		},
		screen: {
			name: 'Screen Time Ending',
			icon: '📱',
			color: '#27ae60',
			defaultDuration: 5,
			songs: [
				{
					id: 'screenend',
					name: 'All Done Song',
					lyrics: [
						'All done with screen time now',
						'Put the device away somehow',
						'Time to play in other ways',
						'Screen time ends, it\'s been a good day!'
					]
				},
				{
					id: 'turnoff',
					name: 'Turn Off Time',
					lyrics: [
						'Turn it off, turn it off',
						'Time is up for now',
						'We can watch again another day',
						'But for now we say hooray to play!'
					]
				}
			]
		}
	};

	$effect(() => {
		if (isRunning && remainingSeconds > 0) {
			intervalId = setInterval(() => {
				remainingSeconds--;
				if (remainingSeconds <= 0) {
					stopTimer();
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
		isRunning = true;
	}

	function pauseTimer() {
		isRunning = false;
	}

	function stopTimer() {
		isRunning = false;
		remainingSeconds = 0;
	}

	function resetTimer() {
		stopTimer();
		remainingSeconds = duration * 60;
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
		selectedSong = transitions[type].songs[0].id;
	}

	function getCurrentSong() {
		return transitions[transitionType].songs.find((s) => s.id === selectedSong);
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
			<label>Duration: {duration} minutes</label>
			<input
				type="range"
				min="1"
				max="15"
				bind:value={duration}
				onchange={resetTimer}
				disabled={isRunning}
			/>
		</div>

		<div class="setting">
			<label>Choose Song:</label>
			<div class="song-buttons">
				{#each transitions[transitionType].songs as song}
					<button
						class="song-button"
						class:active={selectedSong === song.id}
						onclick={() => (selectedSong = song.id)}
						disabled={isRunning}
					>
						{song.name}
					</button>
				{/each}
			</div>
		</div>
	</div>

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

		<div class="song-display">
			<h3>🎵 {getCurrentSong().name}</h3>
			<div class="lyrics">
				{#each getCurrentSong().lyrics as line}
					<p>{line}</p>
				{/each}
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

	{#if remainingSeconds === 0 && !isRunning && duration > 0}
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

	input[type='range'] {
		width: 100%;
	}

	input[type='range']:disabled {
		opacity: 0.5;
	}

	.song-buttons {
		display: flex;
		gap: 1rem;
		flex-wrap: wrap;
	}

	.song-button {
		flex: 1;
		min-width: 150px;
		padding: 0.75rem 1rem;
		border: 2px solid #e0e0e0;
		background: white;
		border-radius: 8px;
		cursor: pointer;
		transition: all 0.3s;
		font-size: 0.95rem;
	}

	.song-button:hover:not(:disabled) {
		border-color: #3498db;
	}

	.song-button.active {
		border-color: #3498db;
		background: #e3f2fd;
		font-weight: 600;
	}

	.song-button:disabled {
		opacity: 0.5;
		cursor: not-allowed;
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

	.song-display {
		text-align: center;
		margin-bottom: 2rem;
		padding: 1.5rem;
		background: #f8f9fa;
		border-radius: 8px;
	}

	.song-display h3 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 1rem;
	}

	.lyrics {
		font-size: 1.1rem;
		line-height: 1.8;
		color: #2c3e50;
		font-style: italic;
	}

	.lyrics p {
		margin: 0.5rem 0;
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

		.song-buttons {
			flex-direction: column;
		}

		.song-button {
			min-width: 100%;
		}

		.timer-controls {
			flex-direction: column;
		}

		.control-button {
			width: 100%;
		}
	}
</style>
