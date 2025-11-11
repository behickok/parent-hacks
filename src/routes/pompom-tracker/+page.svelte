<script>
	import { onMount, untrack } from 'svelte';
	import { goto } from '$app/navigation';

	let kids = $state([]);
	let activeKidId = $state(null);
	let showKidManager = $state(false);
	let showSettings = $state(false);
	let newKidName = $state('');
	let newKidColor = $state('#3b82f6');
	let newKidTheme = $state('pompoms');

	// Active kid's data
	let currentCount = $state(0);
	let initialCount = $state(10);
	let targetCount = $state(20);
	let theme = $state('pompoms');
	let streak = $state(0);
	let lastCountDate = $state(null);
	let lastCountValue = $state(null);

	const themes = {
		pompoms: { emoji: '🎨', name: 'Pom Poms' },
		baseballs: { emoji: '⚾', name: 'Baseballs' },
		unicorns: { emoji: '🦄', name: 'Unicorns' },
		stars: { emoji: '⭐', name: 'Stars' },
		hearts: { emoji: '❤️', name: 'Hearts' },
		flowers: { emoji: '🌸', name: 'Flowers' }
	};

	const colorOptions = [
		'#3b82f6', // Blue
		'#ec4899', // Pink
		'#8b5cf6', // Purple
		'#10b981', // Green
		'#f59e0b', // Orange
		'#ef4444', // Red
		'#06b6d4', // Cyan
		'#f97316'  // Dark Orange
	];

	// Track when we switch kids to reload their data
	let lastLoadedKidId = $state(null);

	$effect(() => {
		// Only reload kid data when we actually switch to a different kid
		if (activeKidId && activeKidId !== lastLoadedKidId) {
			loadKidData(activeKidId);
			lastLoadedKidId = activeKidId;
		}
	});

	onMount(() => {
		if (typeof window !== 'undefined') {
			// Try to load multi-kid data first
			const multiSaved = localStorage.getItem('pompomTrackerMulti');
			if (multiSaved) {
				const data = JSON.parse(multiSaved);
				kids = data.kids || [];
				activeKidId = data.activeKidId;

				// Migration: Add color to existing kids if missing
				let needsSave = false;
				kids = kids.map((kid, index) => {
					if (!kid.color) {
						needsSave = true;
						return { ...kid, color: colorOptions[index % colorOptions.length] };
					}
					return kid;
				});
				if (needsSave) {
					saveToLocalStorage();
				}
			} else {
				// Migration: Check for old single-kid data
				const oldSaved = localStorage.getItem('pompomTracker');
				if (oldSaved) {
					const oldData = JSON.parse(oldSaved);
					// Create a default kid with old data
					const migrationKid = {
						id: crypto.randomUUID(),
						name: 'My Kid',
						color: '#3b82f6',
						currentCount: oldData.currentCount ?? 0,
						initialCount: oldData.initialCount ?? 10,
						targetCount: oldData.targetCount ?? 20,
						theme: oldData.theme ?? 'pompoms',
						createdAt: Date.now()
					};
					kids = [migrationKid];
					activeKidId = migrationKid.id;
					saveToLocalStorage();
					// Remove old storage
					localStorage.removeItem('pompomTracker');
				}
			}

			// If no kids exist, show kid manager
			if (kids.length === 0) {
				showKidManager = true;
			}
		}
	});

	function saveToLocalStorage() {
		if (typeof window !== 'undefined') {
			// Save the active kid's current state back to the kids array
			if (activeKidId) {
				const kidIndex = kids.findIndex((k) => k.id === activeKidId);
				if (kidIndex !== -1) {
					// Create a new array with the updated kid to ensure reactivity
					kids = kids.map((kid, index) => {
						if (index === kidIndex) {
							return {
								...kid,
								currentCount,
								initialCount,
								targetCount,
								theme,
								streak,
								lastCountDate,
								lastCountValue
							};
						}
						return kid;
					});
				}
			}

			localStorage.setItem(
				'pompomTrackerMulti',
				JSON.stringify({ kids, activeKidId })
			);
		}
	}

	function loadKidData(kidId) {
		// Use untrack to prevent this from creating dependencies on kids array
		untrack(() => {
			const kid = kids.find((k) => k.id === kidId);
			if (kid) {
				currentCount = kid.currentCount;
				initialCount = kid.initialCount;
				targetCount = kid.targetCount;
				theme = kid.theme;
				streak = kid.streak || 0;
				lastCountDate = kid.lastCountDate || null;
				lastCountValue = kid.lastCountValue || null;
			}
		});
	}

	function createKid() {
		if (!newKidName.trim()) return;

		const defaultInitialCount = 10;
		const newKid = {
			id: crypto.randomUUID(),
			name: newKidName.trim(),
			color: newKidColor,
			currentCount: defaultInitialCount,
			initialCount: defaultInitialCount,
			targetCount: 20,
			theme: newKidTheme,
			createdAt: Date.now()
		};

		kids = [...kids, newKid];
		activeKidId = newKid.id;
		newKidName = '';
		newKidColor = colorOptions[kids.length % colorOptions.length]; // Set next color
		newKidTheme = 'pompoms'; // Reset to default theme
		showKidManager = false;
		saveToLocalStorage();
	}

	function switchKid(kidId) {
		// Save current kid's data before switching
		if (activeKidId) {
			saveToLocalStorage();
		}
		// Switch to new kid
		activeKidId = kidId;
		showKidManager = false;
	}

	function deleteKid(kidId) {
		if (kids.length === 1) {
			// Don't delete the last kid
			return;
		}

		kids = kids.filter((k) => k.id !== kidId);

		// If we deleted the active kid, switch to the first remaining kid
		if (activeKidId === kidId) {
			activeKidId = kids[0]?.id || null;
		}

		saveToLocalStorage();
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

	function countDay() {
		// Get today's date as a string (YYYY-MM-DD)
		const today = new Date().toISOString().split('T')[0];

		// Check if goal was met today
		const goalMet = currentCount >= targetCount;

		// Update streak logic
		if (goalMet) {
			// Goal met - extend streak
			// Check if we had a previous count date
			if (lastCountDate) {
				// Calculate days between last count and today
				const lastDate = new Date(lastCountDate);
				const todayDate = new Date(today);
				const daysDiff = Math.floor((todayDate - lastDate) / (1000 * 60 * 60 * 24));

				// If we're counting consecutively (within reasonable gap), increment
				// Otherwise, start fresh at 1
				if (daysDiff <= 1) {
					streak++;
				} else {
					streak = 1;
				}
			} else {
				// First time counting - start streak at 1
				streak = 1;
			}
		} else {
			// Goal not met - reset streak to 0
			streak = 0;
		}

		// Save today's count
		lastCountDate = today;
		lastCountValue = currentCount;

		// Reset for next day
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
		<button class="back-button" onclick={() => goto('/')}>↩︎ Back</button>
		<h1>
			{#if activeKidId}
				{kids.find((k) => k.id === activeKidId)?.name}'s {themes[theme].name}
			{:else}
				{themes[theme].name} Tracker
			{/if}
		</h1>
		<div class="header-buttons">
			{#if kids.length > 0}
				<div class="kid-switcher">
					{#each kids as kid (kid.id)}
						<button
							class="kid-switch-button"
							class:active={kid.id === activeKidId}
							onclick={() => switchKid(kid.id)}
							style="background-color: {kid.color};"
							title={kid.name}
						>
							{kid.name.charAt(0).toUpperCase()}
						</button>
					{/each}
					<button class="add-kid-quick-button" onclick={() => (showKidManager = true)} title="Manage Kids">
						+
					</button>
				</div>
			{:else}
				<button class="kids-button" onclick={() => (showKidManager = !showKidManager)}>👦</button>
			{/if}
			<button class="settings-button" onclick={() => (showSettings = !showSettings)}>⚙️</button>
		</div>
	</header>

	{#if showKidManager}
		<div class="kid-manager-panel">
			<h2>Manage Kids</h2>

			<div class="add-kid-form">
				<input
					type="text"
					bind:value={newKidName}
					placeholder="Enter kid's name"
					onkeydown={(e) => e.key === 'Enter' && createKid()}
				/>

				<div class="form-section">
					<label class="form-label">Pick a Color:</label>
					<div class="color-picker">
						{#each colorOptions as color}
							<button
								class="color-option"
								class:selected={newKidColor === color}
								style="background-color: {color};"
								onclick={() => (newKidColor = color)}
								title="Select color"
							></button>
						{/each}
					</div>
				</div>

				<div class="form-section">
					<label class="form-label">Pick a Theme:</label>
					<div class="theme-grid-compact">
						{#each Object.entries(themes) as [key, value]}
							<button
								class="theme-button-compact"
								class:selected={newKidTheme === key}
								onclick={() => (newKidTheme = key)}
							>
								<span class="theme-emoji">{value.emoji}</span>
								<span class="theme-name-compact">{value.name}</span>
							</button>
						{/each}
					</div>
				</div>

				<button class="add-kid-button" onclick={createKid}>Add Kid</button>
			</div>

			{#if kids.length > 0}
				<div class="kids-list">
					<h3>All Kids:</h3>
					{#each kids as kid (kid.id)}
						<div class="kid-item" class:active={kid.id === activeKidId}>
							<div
								class="kid-color-indicator"
								style="background-color: {kid.color};"
							></div>
							<button class="kid-select-button" onclick={() => switchKid(kid.id)}>
								<span class="kid-theme-emoji">{themes[kid.theme].emoji}</span>
								<span class="kid-name">{kid.name}</span>
								<span class="kid-count">{kid.currentCount}/{kid.targetCount}</span>
							</button>
							{#if kids.length > 1}
								<button
									class="delete-kid-button"
									onclick={() => deleteKid(kid.id)}
									title="Delete {kid.name}"
								>
									🗑️
								</button>
							{/if}
						</div>
					{/each}
				</div>
			{/if}

			<button class="close-manager-button" onclick={() => (showKidManager = false)}>Close</button>
		</div>
	{/if}

	{#if showSettings && activeKidId}
		<div class="settings-panel">
			<h2>Settings for {kids.find((k) => k.id === activeKidId)?.name}</h2>

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

	{#if activeKidId}
		<div class="tracker-container">
		<div class="info-banner">
			<span class="info-icon">💡</span>
			<span class="info-text">Reward good behavior! Add one each time your kid does something great.</span>
		</div>

		<div class="streak-display">
			<span class="streak-emoji">🔥</span>
			<span class="streak-number">{streak}</span>
			<span class="streak-label">Day Streak!</span>
		</div>

		<div class="progress-bar">
			<div class="progress-fill" style="width: {(currentCount / targetCount) * 100}%"></div>
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
			<button class="remove-button" onclick={removePompom} disabled={currentCount === 0} title="Remove One">
				➖
			</button>
			<button class="reset-button" onclick={reset} title="Reset">
				↺
			</button>
			<button class="add-button" onclick={addPompom} title="Add One">
				➕
			</button>
			<button class="count-button" onclick={countDay} title="Count & Reset for Tomorrow">
				📊
			</button>
		</div>

		{#if currentCount >= targetCount}
			<div class="celebration">🎉 Goal Reached! 🎉</div>
		{/if}
		</div>
	{:else}
		<div class="no-kid-message">
			<p>No kids added yet!</p>
			<p>Click the 👦 button above to add a kid and start tracking.</p>
		</div>
	{/if}
</div>

<style>
	:global(body) {
		background: linear-gradient(135deg, #f0f9ff via #fdf2f8 to #fef3c7);
	}

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
		color: #1f2937;
		flex: 1;
		text-align: center;
	}

	.header-buttons {
		display: flex;
		gap: 0.5rem;
		align-items: center;
	}

	.back-button,
	.settings-button,
	.kids-button {
		background: transparent;
		color: #374151;
		border: 1px solid #d1d5db;
		padding: 0.75rem 1.5rem;
		border-radius: 9999px;
		cursor: pointer;
		font-size: 1rem;
		transition: all 0.3s;
	}

	.back-button:hover,
	.settings-button:hover,
	.kids-button:hover {
		background: rgba(255, 255, 255, 0.8);
		border-color: #9ca3af;
	}

	.kid-switcher {
		display: flex;
		gap: 0.25rem;
		background: rgba(255, 255, 255, 0.5);
		padding: 0.25rem;
		border-radius: 9999px;
		border: 1px solid #d1d5db;
	}

	.kid-switch-button {
		width: 40px;
		height: 40px;
		border-radius: 50%;
		border: 2px solid transparent;
		color: white;
		font-weight: 700;
		font-size: 1rem;
		cursor: pointer;
		transition: all 0.2s;
		display: flex;
		align-items: center;
		justify-content: center;
		text-shadow: 0 1px 2px rgba(0, 0, 0, 0.3);
	}

	.kid-switch-button:hover {
		transform: scale(1.1);
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
	}

	.kid-switch-button.active {
		border-color: #fff;
		box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.3);
		transform: scale(1.15);
	}

	.add-kid-quick-button {
		width: 40px;
		height: 40px;
		border-radius: 50%;
		border: 2px dashed #9ca3af;
		background: transparent;
		color: #6b7280;
		font-weight: 700;
		font-size: 1.5rem;
		cursor: pointer;
		transition: all 0.2s;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.add-kid-quick-button:hover {
		background: rgba(255, 255, 255, 0.8);
		border-color: #3b82f6;
		color: #3b82f6;
		transform: scale(1.1);
	}

	.kid-manager-panel {
		background: rgba(255, 255, 255, 0.9);
		border: 2px solid #e0e0e0;
		border-radius: 1rem;
		padding: 2rem;
		margin-bottom: 2rem;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
	}

	.kid-manager-panel h2 {
		margin-top: 0;
		color: #1f2937;
	}

	.kid-manager-panel h3 {
		color: #1f2937;
		margin-bottom: 1rem;
	}

	.add-kid-form {
		display: flex;
		flex-direction: column;
		gap: 1rem;
		margin-bottom: 2rem;
	}

	.add-kid-form input {
		width: 100%;
		padding: 0.75rem;
		border: 2px solid #e0e0e0;
		border-radius: 1rem;
		font-size: 1rem;
	}

	.form-section {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
	}

	.form-label {
		font-weight: 600;
		color: #1f2937;
		font-size: 0.9rem;
	}

	.color-picker {
		display: flex;
		gap: 0.5rem;
		flex-wrap: wrap;
		justify-content: center;
		padding: 0.5rem;
		background: rgba(255, 255, 255, 0.5);
		border-radius: 1rem;
	}

	.color-option {
		width: 40px;
		height: 40px;
		border-radius: 50%;
		border: 3px solid transparent;
		cursor: pointer;
		transition: all 0.2s;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
	}

	.color-option:hover {
		transform: scale(1.15);
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
	}

	.color-option.selected {
		border-color: #fff;
		box-shadow: 0 0 0 3px rgba(0, 0, 0, 0.2);
		transform: scale(1.2);
	}

	.theme-grid-compact {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 0.5rem;
	}

	.theme-button-compact {
		background: rgba(255, 255, 255, 0.8);
		border: 2px solid #e0e0e0;
		border-radius: 0.75rem;
		padding: 0.75rem 0.5rem;
		cursor: pointer;
		transition: all 0.3s;
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 0.25rem;
	}

	.theme-button-compact:hover {
		border-color: #3b82f6;
		transform: translateY(-2px);
	}

	.theme-button-compact.selected {
		border-color: #3b82f6;
		background: #e3f2fd;
	}

	.theme-name-compact {
		font-size: 0.75rem;
		color: #1f2937;
	}

	.add-kid-button {
		background: #3b82f6;
		color: white;
		border: none;
		padding: 0.75rem 1.5rem;
		border-radius: 1rem;
		cursor: pointer;
		font-size: 1rem;
		font-weight: 600;
		transition: background 0.3s;
	}

	.add-kid-button:hover {
		background: #2563eb;
	}

	.kids-list {
		margin-bottom: 1.5rem;
	}

	.kid-item {
		display: flex;
		gap: 0.5rem;
		margin-bottom: 0.75rem;
		align-items: stretch;
	}

	.kid-item.active .kid-select-button {
		border-color: #3b82f6;
		background: #e3f2fd;
	}

	.kid-color-indicator {
		width: 8px;
		border-radius: 1rem;
		flex-shrink: 0;
	}

	.kid-select-button {
		flex: 1;
		display: flex;
		align-items: center;
		gap: 1rem;
		padding: 1rem;
		background: rgba(255, 255, 255, 0.8);
		border: 2px solid #e0e0e0;
		border-radius: 1rem;
		cursor: pointer;
		transition: all 0.3s;
		text-align: left;
	}

	.kid-select-button:hover {
		border-color: #3b82f6;
		transform: translateY(-2px);
	}

	.kid-theme-emoji {
		font-size: 1.5rem;
		flex-shrink: 0;
	}

	.kid-name {
		flex: 1;
		font-weight: 600;
		color: #1f2937;
		font-size: 1.1rem;
	}

	.kid-count {
		color: #6b7280;
		font-size: 0.9rem;
	}

	.delete-kid-button {
		background: #e74c3c;
		color: white;
		border: none;
		padding: 0.75rem 1rem;
		border-radius: 1rem;
		cursor: pointer;
		font-size: 1.2rem;
		transition: all 0.3s;
	}

	.delete-kid-button:hover {
		background: #c0392b;
		transform: scale(1.05);
	}

	.close-manager-button {
		width: 100%;
		background: #6b7280;
		color: white;
		border: none;
		padding: 0.75rem;
		border-radius: 1rem;
		cursor: pointer;
		font-size: 1rem;
		font-weight: 600;
		transition: background 0.3s;
	}

	.close-manager-button:hover {
		background: #4b5563;
	}

	.no-kid-message {
		background: rgba(255, 255, 255, 0.8);
		border: 2px dashed #e0e0e0;
		border-radius: 1rem;
		padding: 3rem;
		text-align: center;
		color: #6b7280;
		font-size: 1.1rem;
	}

	.no-kid-message p {
		margin: 0.5rem 0;
	}

	.settings-panel {
		background: rgba(255, 255, 255, 0.8);
		border: 2px solid #e0e0e0;
		border-radius: 1rem;
		padding: 2rem;
		margin-bottom: 2rem;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
	}

	.settings-panel h2 {
		margin-top: 0;
		color: #1f2937;
	}

	.setting {
		margin-bottom: 1.5rem;
	}

	.setting label {
		display: block;
		font-weight: 600;
		color: #1f2937;
		margin-bottom: 0.5rem;
	}

	.setting input[type='number'] {
		width: 100%;
		padding: 0.75rem;
		border: 2px solid #e0e0e0;
		border-radius: 1rem;
		font-size: 1rem;
	}

	.theme-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
		gap: 1rem;
		margin-top: 0.5rem;
	}

	.theme-button {
		background: rgba(255, 255, 255, 0.8);
		border: 2px solid #e0e0e0;
		border-radius: 1rem;
		padding: 1rem;
		cursor: pointer;
		transition: all 0.3s;
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 0.5rem;
	}

	.theme-button:hover {
		border-color: #3b82f6;
		transform: translateY(-2px);
	}

	.theme-button.selected {
		border-color: #3b82f6;
		background: #e3f2fd;
	}

	.theme-emoji {
		font-size: 2rem;
	}

	.theme-name {
		font-size: 0.9rem;
		color: #1f2937;
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
		border-radius: 1rem;
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
		color: #1f2937;
	}

	.cancel-button:hover {
		background: #bdbdbd;
	}

	.tracker-container {
		background: rgba(255, 255, 255, 0.8);
		border-radius: 1rem;
		padding: 2rem;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
	}

	.info-banner {
		background: linear-gradient(135deg, #e0f2fe 0%, #dbeafe 100%);
		border-left: 4px solid #3b82f6;
		border-radius: 0.5rem;
		padding: 0.75rem 1rem;
		margin-bottom: 1.5rem;
		display: flex;
		align-items: center;
		gap: 0.75rem;
		font-size: 0.9rem;
		color: #1e40af;
	}

	.info-icon {
		font-size: 1.25rem;
		flex-shrink: 0;
	}

	.info-text {
		line-height: 1.4;
	}

	.streak-display {
		background: linear-gradient(135deg, #fef3c7 0%, #fde68a 100%);
		border: 3px solid #f59e0b;
		border-radius: 1rem;
		padding: 1rem;
		margin-bottom: 1.5rem;
		display: flex;
		align-items: center;
		justify-content: center;
		gap: 0.75rem;
		font-weight: bold;
	}

	.streak-emoji {
		font-size: 2rem;
	}

	.streak-number {
		font-size: 2.5rem;
		color: #f59e0b;
	}

	.streak-label {
		font-size: 1.1rem;
		color: #92400e;
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
		background: linear-gradient(90deg, #3b82f6, #2ecc71);
		transition: width 0.5s ease;
	}

	.counter {
		text-align: center;
		font-size: 2.5rem;
		font-weight: bold;
		color: #1f2937;
		margin-bottom: 2rem;
	}

	.current {
		color: #3b82f6;
	}

	.separator {
		color: #6b7280;
	}

	.target {
		color: #95a5a6;
	}

	.jar {
		min-height: 300px;
		background: linear-gradient(to bottom, rgba(59, 130, 246, 0.1), rgba(59, 130, 246, 0.2));
		border: 3px solid #3b82f6;
		border-radius: 1.5rem;
		padding: 2rem;
		display: flex;
		flex-wrap: wrap;
		gap: 1rem;
		justify-content: center;
		align-content: flex-start;
		margin-bottom: 2rem;
		box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.08);
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
		border-radius: 1rem;
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
		background: #6b7280;
		transform: translateY(-2px);
	}

	.count-button {
		background: #3b82f6;
		color: white;
	}

	.count-button:hover {
		background: #2563eb;
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
		.jar {
			min-height: 200px;
			max-height: 200px;
			overflow-y: auto;
		}

		.controls {
			flex-direction: row;
			flex-wrap: wrap;
		}

		.controls button {
			flex: 1;
			min-width: 60px;
			padding: 0.75rem 0.5rem;
			font-size: 1.5rem;
		}

		.item {
			font-size: 1.5rem;
		}

		.streak-display {
			padding: 0.75rem;
		}

		.streak-emoji {
			font-size: 1.5rem;
		}

		.streak-number {
			font-size: 2rem;
		}

		.streak-label {
			font-size: 0.9rem;
		}
	}
</style>
