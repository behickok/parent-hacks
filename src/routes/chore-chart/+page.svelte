<script>
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	let childName = $state('');
	let childAge = $state(3);
	let selectedChores = $state([]);
	let completedToday = $state([]);
	let showSuggestions = $state(true);

	const choresByAge = {
		'2-3': [
			{ id: 'toys', name: 'Put toys in bin', icon: '🧸', points: 1 },
			{ id: 'books', name: 'Put books on shelf', icon: '📚', points: 1 },
			{ id: 'clothes-hamper', name: 'Put dirty clothes in hamper', icon: '👕', points: 1 },
			{ id: 'feed-pet', name: 'Help feed pet', icon: '🐕', points: 1 },
			{ id: 'wipe-table', name: 'Wipe table with help', icon: '🧽', points: 1 },
			{ id: 'water-plants', name: 'Water plants (with help)', icon: '🌱', points: 1 }
		],
		'4-5': [
			{ id: 'make-bed', name: 'Make bed', icon: '🛏️', points: 2 },
			{ id: 'set-table', name: 'Set the table', icon: '🍽️', points: 2 },
			{ id: 'clear-dishes', name: 'Clear own dishes', icon: '🍽️', points: 1 },
			{ id: 'match-socks', name: 'Match socks', icon: '🧦', points: 2 },
			{ id: 'sort-laundry', name: 'Sort laundry by color', icon: '👔', points: 2 },
			{ id: 'dust', name: 'Dust low surfaces', icon: '✨', points: 2 },
			{ id: 'water-plants-solo', name: 'Water plants', icon: '🌱', points: 1 },
			{ id: 'feed-pet-solo', name: 'Feed pet', icon: '🐕', points: 2 }
		],
		'6-8': [
			{ id: 'vacuum', name: 'Vacuum room', icon: '🧹', points: 3 },
			{ id: 'load-dishwasher', name: 'Load dishwasher', icon: '🍽️', points: 3 },
			{ id: 'fold-laundry', name: 'Fold laundry', icon: '👕', points: 3 },
			{ id: 'sweep', name: 'Sweep floor', icon: '🧹', points: 2 },
			{ id: 'take-out-trash', name: 'Take out trash', icon: '🗑️', points: 3 },
			{ id: 'pack-lunch', name: 'Pack own lunch', icon: '🍱', points: 3 },
			{ id: 'walk-dog', name: 'Walk dog (with adult)', icon: '🐕', points: 3 },
			{ id: 'organize-backpack', name: 'Organize backpack', icon: '🎒', points: 2 }
		],
		'9+': [
			{ id: 'cook-simple', name: 'Cook simple meal', icon: '🍳', points: 5 },
			{ id: 'wash-dishes', name: 'Wash dishes', icon: '🧼', points: 3 },
			{ id: 'mop-floor', name: 'Mop floor', icon: '🧽', points: 4 },
			{ id: 'wash-car', name: 'Wash car', icon: '🚗', points: 5 },
			{ id: 'yard-work', name: 'Yard work', icon: '🌿', points: 4 },
			{ id: 'babysit', name: 'Watch younger sibling', icon: '👶', points: 5 },
			{ id: 'clean-bathroom', name: 'Clean bathroom', icon: '🚽', points: 4 },
			{ id: 'do-laundry', name: 'Do own laundry', icon: '🧺', points: 4 }
		]
	};

	const allChores = Object.values(choresByAge).flat();

	$effect(() => {
		// Filter available chores based on age
		const ageGroup = getAgeGroup(childAge);
	});

	function getAgeGroup(age) {
		if (age <= 3) return '2-3';
		if (age <= 5) return '4-5';
		if (age <= 8) return '6-8';
		return '9+';
	}

	function getSuggestedChores() {
		const ageGroup = getAgeGroup(childAge);
		const suggested = [];

		// Add chores from current age group
		suggested.push(...choresByAge[ageGroup]);

		// Add some easier chores from previous age groups
		if (ageGroup === '4-5') {
			suggested.push(...choresByAge['2-3']);
		} else if (ageGroup === '6-8') {
			suggested.push(...choresByAge['4-5'], ...choresByAge['2-3']);
		} else if (ageGroup === '9+') {
			suggested.push(...choresByAge['6-8'], ...choresByAge['4-5']);
		}

		// Remove duplicates
		return Array.from(new Map(suggested.map((chore) => [chore.id, chore])).values());
	}

	onMount(() => {
		const saved = localStorage.getItem('choreChart');
		if (saved) {
			const data = JSON.parse(saved);
			if (data.childName) childName = data.childName;
			if (data.childAge) childAge = data.childAge;
			if (data.selectedChores) selectedChores = data.selectedChores;
			if (data.completedToday) {
				const savedDate = data.completedDate;
				const today = new Date().toDateString();
				if (savedDate === today) {
					completedToday = data.completedToday;
				} else {
					completedToday = [];
				}
			}
		}
	});

	function saveToLocalStorage() {
		localStorage.setItem(
			'choreChart',
			JSON.stringify({
				childName,
				childAge,
				selectedChores,
				completedToday,
				completedDate: new Date().toDateString()
			})
		);
	}

	function toggleChore(chore) {
		const index = selectedChores.findIndex((c) => c.id === chore.id);
		if (index > -1) {
			selectedChores = selectedChores.filter((c) => c.id !== chore.id);
			completedToday = completedToday.filter((id) => id !== chore.id);
		} else {
			selectedChores = [...selectedChores, chore];
		}
		saveToLocalStorage();
	}

	function toggleComplete(choreId) {
		if (completedToday.includes(choreId)) {
			completedToday = completedToday.filter((id) => id !== choreId);
		} else {
			completedToday = [...completedToday, choreId];
		}
		saveToLocalStorage();
	}

	function getTotalPoints() {
		return selectedChores
			.filter((chore) => completedToday.includes(chore.id))
			.reduce((sum, chore) => sum + chore.points, 0);
	}

	function getMaxPoints() {
		return selectedChores.reduce((sum, chore) => sum + chore.points, 0);
	}

	function resetDay() {
		completedToday = [];
		saveToLocalStorage();
	}

	function clearAll() {
		selectedChores = [];
		completedToday = [];
		childName = '';
		saveToLocalStorage();
	}

	function printChart() {
		window.print();
	}
</script>

<div class="container">
	<header class="no-print">
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>Chore Chart Generator</h1>
		<div style="width: 100px;"></div>
	</header>

	<div class="intro no-print">
		<p>Create a personalized chore chart with age-appropriate tasks!</p>
	</div>

	<div class="settings-section no-print">
		<div class="setting">
			<label>Child's Name:</label>
			<input type="text" bind:value={childName} onchange={saveToLocalStorage} placeholder="Enter name" />
		</div>

		<div class="setting">
			<label>Child's Age: {childAge} years old</label>
			<input
				type="range"
				min="2"
				max="12"
				bind:value={childAge}
				onchange={saveToLocalStorage}
			/>
		</div>

		<div class="setting">
			<label>
				<input type="checkbox" bind:checked={showSuggestions} />
				Show age-appropriate suggestions
			</label>
		</div>
	</div>

	<div class="chores-selection no-print">
		<h2>
			{showSuggestions ? 'Suggested Chores' : 'All Chores'}
			<span class="count">({selectedChores.length} selected)</span>
		</h2>
		<p class="helper-text">Click to add/remove chores from your chart:</p>

		<div class="chores-grid">
			{#each showSuggestions ? getSuggestedChores() : allChores as chore}
				<button
					class="chore-card"
					class:selected={selectedChores.some((c) => c.id === chore.id)}
					onclick={() => toggleChore(chore)}
				>
					<span class="chore-icon">{chore.icon}</span>
					<span class="chore-name">{chore.name}</span>
					<span class="chore-points">{chore.points} pt{chore.points > 1 ? 's' : ''}</span>
					{#if selectedChores.some((c) => c.id === chore.id)}
						<span class="checkmark">✓</span>
					{/if}
				</button>
			{/each}
		</div>
	</div>

	{#if selectedChores.length > 0}
		<div class="chart-section">
			<div class="chart-header no-print">
				<h2>Chore Chart</h2>
				<div class="chart-actions">
					<button class="action-btn" onclick={printChart}>🖨️ Print</button>
					<button class="action-btn reset" onclick={resetDay}>Reset Today</button>
					<button class="action-btn danger" onclick={clearAll}>Clear All</button>
				</div>
			</div>

			<div class="chart-display">
				{#if childName}
					<h1 class="chart-title">{childName}'s Chore Chart</h1>
				{:else}
					<h1 class="chart-title">My Chore Chart</h1>
				{/if}

				<div class="points-display">
					<div class="points-earned">
						<span class="points-number">{getTotalPoints()}</span>
						<span class="points-label">Points Earned Today</span>
					</div>
					<div class="points-possible">
						<span class="points-number">{getMaxPoints()}</span>
						<span class="points-label">Total Possible</span>
					</div>
				</div>

				<div class="progress-bar-container">
					<div
						class="progress-bar"
						style="width: {getMaxPoints() > 0 ? (getTotalPoints() / getMaxPoints()) * 100 : 0}%"
					></div>
				</div>

				<div class="chore-list">
					{#each selectedChores as chore}
						<button
							class="chore-item"
							class:completed={completedToday.includes(chore.id)}
							onclick={() => toggleComplete(chore.id)}
						>
							<div class="chore-checkbox">
								{#if completedToday.includes(chore.id)}
									<span class="check">✓</span>
								{/if}
							</div>
							<div class="chore-icon-large">{chore.icon}</div>
							<div class="chore-name-large">{chore.name}</div>
							<div class="chore-points-badge">+{chore.points}</div>
						</button>
					{/each}
				</div>

				{#if getTotalPoints() === getMaxPoints() && getMaxPoints() > 0}
					<div class="celebration">
						<h2>🎉 All Chores Complete! 🎉</h2>
						<p>Great job!</p>
					</div>
				{/if}
			</div>
		</div>

		<div class="tips-box no-print">
			<h3>💡 Tips for Success:</h3>
			<ul>
				<li>Start with just 3-5 chores for younger children</li>
				<li>Demonstrate each chore and practice together first</li>
				<li>Set a consistent time for chores each day</li>
				<li>Celebrate effort, not just completion</li>
				<li>Review and adjust chores as your child grows</li>
				<li>Use points for privileges (extra screen time, choosing dinner, etc.)</li>
			</ul>
		</div>
	{:else}
		<div class="empty-state no-print">
			<p>👆 Select some chores above to create your chart!</p>
		</div>
	{/if}
</div>

<style>
	.container {
		max-width: 1000px;
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
	}

	.settings-section {
		background: white;
		padding: 1.5rem;
		border-radius: 12px;
		margin-bottom: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.setting {
		margin-bottom: 1rem;
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

	.setting input[type='text'] {
		width: 100%;
		padding: 0.75rem;
		border: 2px solid #e0e0e0;
		border-radius: 8px;
		font-size: 1rem;
	}

	.setting input[type='range'] {
		width: 100%;
	}

	.setting input[type='checkbox'] {
		margin-right: 0.5rem;
	}

	.chores-selection {
		background: white;
		padding: 1.5rem;
		border-radius: 12px;
		margin-bottom: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.chores-selection h2 {
		margin-top: 0;
		color: #2c3e50;
		display: flex;
		align-items: center;
		gap: 0.5rem;
	}

	.count {
		font-size: 1rem;
		color: #7f8c8d;
		font-weight: normal;
	}

	.helper-text {
		color: #7f8c8d;
		margin-bottom: 1rem;
	}

	.chores-grid {
		display: grid;
		grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
		gap: 1rem;
	}

	.chore-card {
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
		position: relative;
	}

	.chore-card:hover {
		border-color: #3498db;
		transform: translateY(-2px);
	}

	.chore-card.selected {
		border-color: #27ae60;
		background: #e8f8f5;
	}

	.chore-icon {
		font-size: 2.5rem;
	}

	.chore-name {
		font-size: 0.9rem;
		color: #2c3e50;
		text-align: center;
	}

	.chore-points {
		font-size: 0.8rem;
		color: #7f8c8d;
		font-weight: 600;
	}

	.checkmark {
		position: absolute;
		top: 0.5rem;
		right: 0.5rem;
		background: #27ae60;
		color: white;
		width: 24px;
		height: 24px;
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 0.9rem;
	}

	.chart-section {
		background: white;
		padding: 2rem;
		border-radius: 12px;
		margin-bottom: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.chart-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 2rem;
		flex-wrap: wrap;
		gap: 1rem;
	}

	.chart-header h2 {
		margin: 0;
		color: #2c3e50;
	}

	.chart-actions {
		display: flex;
		gap: 0.5rem;
	}

	.action-btn {
		padding: 0.5rem 1rem;
		border: none;
		border-radius: 6px;
		cursor: pointer;
		font-size: 0.9rem;
		transition: all 0.3s;
		background: #3498db;
		color: white;
	}

	.action-btn:hover {
		background: #2980b9;
	}

	.action-btn.reset {
		background: #f39c12;
	}

	.action-btn.reset:hover {
		background: #e67e22;
	}

	.action-btn.danger {
		background: #e74c3c;
	}

	.action-btn.danger:hover {
		background: #c0392b;
	}

	.chart-title {
		text-align: center;
		color: #2c3e50;
		font-size: 2.5rem;
		margin-bottom: 2rem;
	}

	.points-display {
		display: flex;
		justify-content: center;
		gap: 3rem;
		margin-bottom: 1rem;
	}

	.points-earned,
	.points-possible {
		text-align: center;
	}

	.points-number {
		display: block;
		font-size: 3rem;
		font-weight: bold;
		color: #27ae60;
	}

	.points-possible .points-number {
		color: #7f8c8d;
	}

	.points-label {
		display: block;
		color: #7f8c8d;
		font-size: 0.9rem;
	}

	.progress-bar-container {
		width: 100%;
		height: 30px;
		background: #e0e0e0;
		border-radius: 15px;
		overflow: hidden;
		margin-bottom: 2rem;
	}

	.progress-bar {
		height: 100%;
		background: linear-gradient(90deg, #27ae60, #2ecc71);
		transition: width 0.5s ease;
	}

	.chore-list {
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}

	.chore-item {
		display: flex;
		align-items: center;
		gap: 1rem;
		padding: 1rem;
		background: #f8f9fa;
		border: 2px solid #e0e0e0;
		border-radius: 8px;
		cursor: pointer;
		transition: all 0.3s;
		text-align: left;
	}

	.chore-item:hover {
		border-color: #3498db;
	}

	.chore-item.completed {
		background: #e8f8f5;
		border-color: #27ae60;
	}

	.chore-checkbox {
		width: 40px;
		height: 40px;
		border: 3px solid #bdc3c7;
		border-radius: 8px;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-shrink: 0;
		transition: all 0.3s;
	}

	.chore-item.completed .chore-checkbox {
		background: #27ae60;
		border-color: #27ae60;
	}

	.check {
		color: white;
		font-size: 1.5rem;
		font-weight: bold;
	}

	.chore-icon-large {
		font-size: 2.5rem;
	}

	.chore-name-large {
		flex: 1;
		font-size: 1.3rem;
		color: #2c3e50;
		font-weight: 500;
	}

	.chore-item.completed .chore-name-large {
		text-decoration: line-through;
		opacity: 0.7;
	}

	.chore-points-badge {
		background: #f39c12;
		color: white;
		padding: 0.5rem 1rem;
		border-radius: 20px;
		font-weight: 600;
		font-size: 1.1rem;
	}

	.celebration {
		margin-top: 2rem;
		padding: 2rem;
		background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
		border-radius: 12px;
		text-align: center;
		color: white;
	}

	.celebration h2 {
		font-size: 2rem;
		margin: 0 0 0.5rem 0;
	}

	.celebration p {
		font-size: 1.3rem;
		margin: 0;
	}

	.empty-state {
		text-align: center;
		padding: 3rem;
		color: #7f8c8d;
		font-size: 1.3rem;
		background: white;
		border-radius: 12px;
		margin-bottom: 2rem;
	}

	.tips-box {
		background: #e8f4f8;
		padding: 1.5rem;
		border-radius: 12px;
		border-left: 4px solid #3498db;
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

	@media print {
		.no-print {
			display: none !important;
		}

		.chore-item {
			page-break-inside: avoid;
		}
	}

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}

		.chores-grid {
			grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
		}

		.points-display {
			flex-direction: column;
			gap: 1rem;
		}

		.chart-actions {
			width: 100%;
			justify-content: stretch;
		}

		.action-btn {
			flex: 1;
		}
	}
</style>
