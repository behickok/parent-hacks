<script>
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	let currentCount = $state(0);
	let initialCount = $state(10);
	let targetCount = $state(20);
	let theme = $state('pompoms');
	let showSettings = $state(false);

	const themes = {
		pompoms: { emoji: '🎨', name: 'Pom Poms' },
		baseballs: { emoji: '⚾', name: 'Baseballs' },
		unicorns: { emoji: '🦄', name: 'Unicorns' },
		stars: { emoji: '⭐', name: 'Stars' },
		hearts: { emoji: '❤️', name: 'Hearts' },
		flowers: { emoji: '🌸', name: 'Flowers' }
	};

	onMount(() => {
		if (typeof window !== 'undefined') {
			const saved = localStorage.getItem('pompomTracker');
			if (saved) {
				const data = JSON.parse(saved);
				currentCount = data.currentCount ?? initialCount;
				initialCount = data.initialCount ?? 10;
				targetCount = data.targetCount ?? 20;
				theme = data.theme ?? 'pompoms';
			} else {
				currentCount = initialCount;
			}
		}
	});

	function saveToLocalStorage() {
		if (typeof window !== 'undefined') {
			localStorage.setItem(
				'pompomTracker',
				JSON.stringify({ currentCount, initialCount, targetCount, theme })
			);
		}
	}

	function addPompom() {
		currentCount++;
		saveToLocalStorage();
	}

	function removePompom() {
		if (currentCount > 0) {
			currentCount--;
			saveToLocalStorage();
		}
	}

	function reset() {
		currentCount = initialCount;
		saveToLocalStorage();
	}

	function saveSettings() {
		if (currentCount > targetCount) {
			currentCount = targetCount;
		}
		if (currentCount < 0) {
			currentCount = 0;
		}
		saveToLocalStorage();
		showSettings = false;
	}
</script>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>{themes[theme].name} Tracker</h1>
		<button class="settings-button" onclick={() => (showSettings = !showSettings)}>⚙️</button>
	</header>

	{#if showSettings}
		<div class="settings-panel">
			<h2>Settings</h2>

			<div class="setting">
				<label>
					Initial Count:
					<input type="number" bind:value={initialCount} min="0" max="100" />
				</label>
			</div>

			<div class="setting">
				<label>
					Target Count:
					<input type="number" bind:value={targetCount} min="1" max="100" />
				</label>
			</div>

			<div class="setting">
				<label>Theme:</label>
				<div class="theme-grid">
					{#each Object.entries(themes) as [key, value]}
						<button
							class="theme-button"
							class:selected={theme === key}
							onclick={() => (theme = key)}
						>
							<span class="theme-emoji">{value.emoji}</span>
							<span class="theme-name">{value.name}</span>
						</button>
					{/each}
				</div>
			</div>

			<div class="setting-actions">
				<button class="save-button" onclick={saveSettings}>Save Settings</button>
				<button class="cancel-button" onclick={() => (showSettings = false)}>Cancel</button>
			</div>
		</div>
	{/if}

	<div class="tracker-container">
		<div class="progress-bar">
			<div
				class="progress-fill"
				style="width: {(currentCount / targetCount) * 100}%"
			></div>
		</div>

		<div class="counter">
			<span class="current">{currentCount}</span>
			<span class="separator">/</span>
			<span class="target">{targetCount}</span>
		</div>

		<div class="jar">
			{#each Array(currentCount) as _, i (i)}
				<span class="item" style="--delay: {i * 0.05}s">{themes[theme].emoji}</span>
			{/each}
		</div>

		<div class="controls">
			<button class="remove-button" onclick={removePompom} disabled={currentCount === 0}>
				Remove One
			</button>
			<button class="reset-button" onclick={reset}>Reset</button>
			<button class="add-button" onclick={addPompom}>
				Add One
			</button>
		</div>

		{#if currentCount >= targetCount}
			<div class="celebration">🎉 Goal Reached! 🎉</div>
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

	.back-button,
	.settings-button {
		background: #3498db;
		color: white;
		border: none;
		padding: 0.75rem 1.5rem;
		border-radius: 8px;
		cursor: pointer;
		font-size: 1rem;
		transition: background 0.3s;
	}

	.back-button:hover,
	.settings-button:hover {
		background: #2980b9;
	}

	.settings-panel {
		background: white;
		border: 2px solid #e0e0e0;
		border-radius: 12px;
		padding: 2rem;
		margin-bottom: 2rem;
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
	}

	.settings-panel h2 {
		margin-top: 0;
		color: #2c3e50;
	}

	.setting {
		margin-bottom: 1.5rem;
	}

	.setting label {
		display: block;
		font-weight: 600;
		color: #2c3e50;
		margin-bottom: 0.5rem;
	}

	.setting input[type='number'] {
		width: 100%;
		padding: 0.75rem;
		border: 2px solid #e0e0e0;
		border-radius: 8px;
		font-size: 1rem;
	}

	.theme-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
		gap: 1rem;
		margin-top: 0.5rem;
	}

	.theme-button {
		background: white;
		border: 2px solid #e0e0e0;
		border-radius: 8px;
		padding: 1rem;
		cursor: pointer;
		transition: all 0.3s;
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 0.5rem;
	}

	.theme-button:hover {
		border-color: #3498db;
		transform: translateY(-2px);
	}

	.theme-button.selected {
		border-color: #3498db;
		background: #e3f2fd;
	}

	.theme-emoji {
		font-size: 2rem;
	}

	.theme-name {
		font-size: 0.9rem;
		color: #2c3e50;
	}

	.setting-actions {
		display: flex;
		gap: 1rem;
		margin-top: 1.5rem;
	}

	.save-button,
	.cancel-button {
		flex: 1;
		padding: 0.75rem;
		border: none;
		border-radius: 8px;
		font-size: 1rem;
		cursor: pointer;
		transition: background 0.3s;
	}

	.save-button {
		background: #27ae60;
		color: white;
	}

	.save-button:hover {
		background: #229954;
	}

	.cancel-button {
		background: #e0e0e0;
		color: #2c3e50;
	}

	.cancel-button:hover {
		background: #bdbdbd;
	}

	.tracker-container {
		background: white;
		border-radius: 12px;
		padding: 2rem;
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
	}

	.progress-bar {
		width: 100%;
		height: 20px;
		background: #e0e0e0;
		border-radius: 10px;
		overflow: hidden;
		margin-bottom: 1rem;
	}

	.progress-fill {
		height: 100%;
		background: linear-gradient(90deg, #3498db, #2ecc71);
		transition: width 0.5s ease;
	}

	.counter {
		text-align: center;
		font-size: 2.5rem;
		font-weight: bold;
		color: #2c3e50;
		margin-bottom: 2rem;
	}

	.current {
		color: #3498db;
	}

	.separator {
		color: #7f8c8d;
	}

	.target {
		color: #95a5a6;
	}

	.jar {
		min-height: 300px;
		background: linear-gradient(to bottom, rgba(52, 152, 219, 0.1), rgba(52, 152, 219, 0.2));
		border: 3px solid #3498db;
		border-radius: 20px;
		padding: 2rem;
		display: flex;
		flex-wrap: wrap;
		gap: 1rem;
		justify-content: center;
		align-content: flex-start;
		margin-bottom: 2rem;
		box-shadow: inset 0 4px 8px rgba(0, 0, 0, 0.1);
	}

	.item {
		font-size: 2.5rem;
		animation: pop-in 0.3s ease backwards;
		animation-delay: var(--delay);
	}

	@keyframes pop-in {
		0% {
			transform: scale(0);
			opacity: 0;
		}
		50% {
			transform: scale(1.2);
		}
		100% {
			transform: scale(1);
			opacity: 1;
		}
	}

	.controls {
		display: flex;
		gap: 1rem;
		justify-content: center;
	}

	.controls button {
		padding: 1rem 2rem;
		font-size: 1.1rem;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		transition: all 0.3s;
		font-weight: 600;
	}

	.add-button {
		background: #27ae60;
		color: white;
	}

	.add-button:hover:not(:disabled) {
		background: #229954;
		transform: translateY(-2px);
	}

	.remove-button {
		background: #e74c3c;
		color: white;
	}

	.remove-button:hover:not(:disabled) {
		background: #c0392b;
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

	.controls button:disabled {
		opacity: 0.5;
		cursor: not-allowed;
	}

	.celebration {
		margin-top: 2rem;
		text-align: center;
		font-size: 2rem;
		font-weight: bold;
		color: #f39c12;
		animation: bounce 1s infinite;
	}

	@keyframes bounce {
		0%,
		100% {
			transform: translateY(0);
		}
		50% {
			transform: translateY(-10px);
		}
	}

	@media (max-width: 600px) {
		.controls {
			flex-direction: column;
		}

		.controls button {
			width: 100%;
		}

		.item {
			font-size: 2rem;
		}
	}
</style>
