<script>
	import { onMount } from 'svelte';
	import { goto } from '$app/navigation';

	let routineType = $state('morning');
	let routineName = $state('');
	let selectedSteps = $state([]);
	let customStep = $state('');
	let showPreview = $state(false);

	const availableSteps = {
		morning: [
			{ icon: '🌅', name: 'Wake up', id: 'wakeup' },
			{ icon: '🚽', name: 'Use the potty/bathroom', id: 'potty' },
			{ icon: '🧼', name: 'Wash hands', id: 'washhands' },
			{ icon: '🪥', name: 'Brush teeth', id: 'brushteeth' },
			{ icon: '🧖', name: 'Wash face', id: 'washface' },
			{ icon: '👕', name: 'Get dressed', id: 'getdressed' },
			{ icon: '🥣', name: 'Eat breakfast', id: 'breakfast' },
			{ icon: '💊', name: 'Take medicine/vitamins', id: 'medicine' },
			{ icon: '🎒', name: 'Pack backpack', id: 'packbag' },
			{ icon: '👞', name: 'Put on shoes', id: 'shoes' },
			{ icon: '🧥', name: 'Put on coat', id: 'coat' },
			{ icon: '📚', name: 'Put homework in bag', id: 'homework' }
		],
		bedtime: [
			{ icon: '🧹', name: 'Clean up toys', id: 'cleanup' },
			{ icon: '🍽️', name: 'Eat dinner', id: 'dinner' },
			{ icon: '🛁', name: 'Take a bath', id: 'bath' },
			{ icon: '👕', name: 'Put on pajamas', id: 'pajamas' },
			{ icon: '🪥', name: 'Brush teeth', id: 'brushteeth' },
			{ icon: '🚽', name: 'Use the potty/bathroom', id: 'potty' },
			{ icon: '📖', name: 'Read a story', id: 'story' },
			{ icon: '🎵', name: 'Sing a lullaby', id: 'lullaby' },
			{ icon: '🤗', name: 'Hugs and kisses', id: 'hugs' },
			{ icon: '💡', name: 'Turn off lights', id: 'lights' },
			{ icon: '😴', name: 'Sleep time', id: 'sleep' }
		],
		afterschool: [
			{ icon: '🏠', name: 'Arrive home', id: 'home' },
			{ icon: '🧥', name: 'Hang up coat', id: 'hangcoat' },
			{ icon: '👞', name: 'Put away shoes', id: 'putshoes' },
			{ icon: '🎒', name: 'Unpack backpack', id: 'unpack' },
			{ icon: '🧼', name: 'Wash hands', id: 'washhands' },
			{ icon: '🍎', name: 'Eat a snack', id: 'snack' },
			{ icon: '📝', name: 'Do homework', id: 'homework' },
			{ icon: '🎮', name: 'Free play time', id: 'play' },
			{ icon: '📺', name: 'Screen time', id: 'screen' },
			{ icon: '🧹', name: 'Help with chores', id: 'chores' },
			{ icon: '🍽️', name: 'Set the table', id: 'settable' }
		]
	};

	onMount(() => {
		const saved = localStorage.getItem('routineBuilder');
		if (saved) {
			const data = JSON.parse(saved);
			if (data.routineName) routineName = data.routineName;
			if (data.routineType) routineType = data.routineType;
			if (data.selectedSteps) selectedSteps = data.selectedSteps;
		}
	});

	function saveToLocalStorage() {
		localStorage.setItem(
			'routineBuilder',
			JSON.stringify({ routineName, routineType, selectedSteps })
		);
	}

	function toggleStep(step) {
		const index = selectedSteps.findIndex((s) => s.id === step.id);
		if (index > -1) {
			selectedSteps = selectedSteps.filter((s) => s.id !== step.id);
		} else {
			selectedSteps = [...selectedSteps, step];
		}
		saveToLocalStorage();
	}

	function addCustomStep() {
		if (customStep.trim()) {
			const newStep = {
				icon: '✨',
				name: customStep.trim(),
				id: `custom-${Date.now()}`
			};
			selectedSteps = [...selectedSteps, newStep];
			customStep = '';
			saveToLocalStorage();
		}
	}

	function moveUp(index) {
		if (index > 0) {
			const newSteps = [...selectedSteps];
			[newSteps[index - 1], newSteps[index]] = [newSteps[index], newSteps[index - 1]];
			selectedSteps = newSteps;
			saveToLocalStorage();
		}
	}

	function moveDown(index) {
		if (index < selectedSteps.length - 1) {
			const newSteps = [...selectedSteps];
			[newSteps[index], newSteps[index + 1]] = [newSteps[index + 1], newSteps[index]];
			selectedSteps = newSteps;
			saveToLocalStorage();
		}
	}

	function removeStep(id) {
		selectedSteps = selectedSteps.filter((s) => s.id !== id);
		saveToLocalStorage();
	}

	function clearAll() {
		selectedSteps = [];
		routineName = '';
		saveToLocalStorage();
	}

	function changeRoutineType(type) {
		routineType = type;
		saveToLocalStorage();
	}

	function printRoutine() {
		window.print();
	}
</script>

<div class="container">
	<header class="no-print">
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>Routine Builder</h1>
		<div style="width: 100px;"></div>
	</header>

	<div class="intro no-print">
		<p>Create a visual routine to help your child know what to expect and stay on track!</p>
	</div>

	<div class="builder-section no-print">
		<div class="routine-settings">
			<div class="setting">
				<label>Routine Name:</label>
				<input
					type="text"
					bind:value={routineName}
					onchange={saveToLocalStorage}
					placeholder="e.g., Emma's Morning Routine"
				/>
			</div>

			<div class="setting">
				<label>Routine Type:</label>
				<div class="routine-type-buttons">
					<button
						class="type-button"
						class:active={routineType === 'morning'}
						onclick={() => changeRoutineType('morning')}
					>
						🌅 Morning
					</button>
					<button
						class="type-button"
						class:active={routineType === 'afterschool'}
						onclick={() => changeRoutineType('afterschool')}
					>
						🏠 After School
					</button>
					<button
						class="type-button"
						class:active={routineType === 'bedtime'}
						onclick={() => changeRoutineType('bedtime')}
					>
						🌙 Bedtime
					</button>
				</div>
			</div>
		</div>

		<div class="steps-section">
			<h2>Available Steps</h2>
			<p class="helper-text">Click to add steps to your routine:</p>
			<div class="available-steps">
				{#each availableSteps[routineType] as step}
					<button
						class="step-card"
						class:selected={selectedSteps.some((s) => s.id === step.id)}
						onclick={() => toggleStep(step)}
					>
						<span class="step-icon">{step.icon}</span>
						<span class="step-name">{step.name}</span>
						{#if selectedSteps.some((s) => s.id === step.id)}
							<span class="checkmark">✓</span>
						{/if}
					</button>
				{/each}
			</div>

			<div class="custom-step">
				<h3>Add Custom Step</h3>
				<div class="custom-input">
					<input
						type="text"
						bind:value={customStep}
						placeholder="Enter custom step..."
						onkeydown={(e) => e.key === 'Enter' && addCustomStep()}
					/>
					<button onclick={addCustomStep}>Add</button>
				</div>
			</div>
		</div>
	</div>

	{#if selectedSteps.length > 0}
		<div class="routine-preview">
			<div class="preview-header no-print">
				<h2>Your Routine</h2>
				<div class="preview-actions">
					<button class="preview-button" onclick={() => (showPreview = !showPreview)}>
						{showPreview ? 'Edit Mode' : 'Preview Mode'}
					</button>
					<button class="print-button" onclick={printRoutine}>🖨️ Print</button>
					<button class="clear-button" onclick={clearAll}>Clear All</button>
				</div>
			</div>

			<div class="routine-display" class:preview-mode={showPreview}>
				{#if routineName}
					<h1 class="routine-title">{routineName}</h1>
				{/if}

				<div class="routine-steps">
					{#each selectedSteps as step, index}
						<div class="routine-step">
							<div class="step-number">{index + 1}</div>
							<div class="step-content">
								<div class="step-icon-large">{step.icon}</div>
								<div class="step-name-large">{step.name}</div>
							</div>
							{#if !showPreview}
								<div class="step-controls">
									<button
										class="control-btn"
										onclick={() => moveUp(index)}
										disabled={index === 0}
										title="Move up"
									>
										↑
									</button>
									<button
										class="control-btn"
										onclick={() => moveDown(index)}
										disabled={index === selectedSteps.length - 1}
										title="Move down"
									>
										↓
									</button>
									<button
										class="control-btn remove"
										onclick={() => removeStep(step.id)}
										title="Remove"
									>
										×
									</button>
								</div>
							{/if}
						</div>
					{/each}
				</div>
			</div>
		</div>
	{:else}
		<div class="empty-state no-print">
			<p>👆 Click on steps above to start building your routine!</p>
		</div>
	{/if}

	<div class="tips-box no-print">
		<h3>💡 Tips for Success:</h3>
		<ul>
			<li>Keep routines short (5-8 steps) for younger children</li>
			<li>Use pictures/icons so non-readers can follow along</li>
			<li>Practice the routine together during calm times</li>
			<li>Print and post at child's eye level</li>
			<li>Celebrate when steps are completed!</li>
			<li>Be consistent - use the same routine every day</li>
		</ul>
	</div>
</div>

<style>
	.container {
		max-width: 1200px;
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

	.routine-settings {
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

	.setting input {
		width: 100%;
		padding: 0.75rem;
		border: 2px solid #e0e0e0;
		border-radius: 8px;
		font-size: 1rem;
	}

	.routine-type-buttons {
		display: flex;
		gap: 1rem;
		flex-wrap: wrap;
	}

	.type-button {
		flex: 1;
		min-width: 150px;
		padding: 1rem;
		border: 2px solid #e0e0e0;
		background: white;
		border-radius: 8px;
		cursor: pointer;
		font-size: 1.1rem;
		transition: all 0.3s;
	}

	.type-button:hover {
		border-color: #3498db;
	}

	.type-button.active {
		border-color: #3498db;
		background: #e3f2fd;
		font-weight: 600;
	}

	.steps-section {
		background: white;
		padding: 1.5rem;
		border-radius: 12px;
		margin-bottom: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.steps-section h2 {
		margin-top: 0;
		color: #2c3e50;
	}

	.helper-text {
		color: #7f8c8d;
		margin-bottom: 1rem;
	}

	.available-steps {
		display: grid;
		grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
		gap: 1rem;
		margin-bottom: 2rem;
	}

	.step-card {
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

	.step-card:hover {
		border-color: #3498db;
		transform: translateY(-2px);
	}

	.step-card.selected {
		border-color: #27ae60;
		background: #e8f8f5;
	}

	.step-icon {
		font-size: 2.5rem;
	}

	.step-name {
		font-size: 0.9rem;
		color: #2c3e50;
		text-align: center;
		word-wrap: break-word;
		overflow-wrap: break-word;
		hyphens: auto;
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

	.custom-step {
		border-top: 2px solid #e0e0e0;
		padding-top: 1.5rem;
	}

	.custom-step h3 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 1rem;
	}

	.custom-input {
		display: flex;
		gap: 1rem;
	}

	.custom-input input {
		flex: 1;
		padding: 0.75rem;
		border: 2px solid #e0e0e0;
		border-radius: 8px;
		font-size: 1rem;
	}

	.custom-input button {
		padding: 0.75rem 1.5rem;
		background: #27ae60;
		color: white;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		font-weight: 600;
		transition: background 0.3s;
	}

	.custom-input button:hover {
		background: #229954;
	}

	.routine-preview {
		background: white;
		padding: 2rem;
		border-radius: 12px;
		margin-bottom: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.preview-header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 2rem;
		flex-wrap: wrap;
		gap: 1rem;
	}

	.preview-header h2 {
		margin: 0;
		color: #2c3e50;
	}

	.preview-actions {
		display: flex;
		gap: 0.5rem;
	}

	.preview-button,
	.print-button,
	.clear-button {
		padding: 0.5rem 1rem;
		border: none;
		border-radius: 6px;
		cursor: pointer;
		font-size: 0.9rem;
		transition: all 0.3s;
	}

	.preview-button {
		background: #3498db;
		color: white;
	}

	.preview-button:hover {
		background: #2980b9;
	}

	.print-button {
		background: #9b59b6;
		color: white;
	}

	.print-button:hover {
		background: #8e44ad;
	}

	.clear-button {
		background: #e74c3c;
		color: white;
	}

	.clear-button:hover {
		background: #c0392b;
	}

	.routine-title {
		text-align: center;
		color: #2c3e50;
		font-size: 2.5rem;
		margin-bottom: 2rem;
	}

	.routine-steps {
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}

	.routine-step {
		display: flex;
		align-items: center;
		gap: 1rem;
		padding: 1rem;
		background: #f8f9fa;
		border-radius: 8px;
		border: 2px solid #e0e0e0;
	}

	.routine-display.preview-mode .routine-step {
		padding: 1.5rem;
		background: white;
		border: 3px solid #3498db;
	}

	.step-number {
		background: #3498db;
		color: white;
		width: 40px;
		height: 40px;
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
		font-weight: bold;
		font-size: 1.2rem;
		flex-shrink: 0;
	}

	.routine-display.preview-mode .step-number {
		width: 60px;
		height: 60px;
		font-size: 1.8rem;
	}

	.step-content {
		flex: 1;
		display: flex;
		align-items: center;
		gap: 1rem;
	}

	.step-icon-large {
		font-size: 3rem;
	}

	.routine-display.preview-mode .step-icon-large {
		font-size: 4rem;
	}

	.step-name-large {
		font-size: 1.3rem;
		color: #2c3e50;
		font-weight: 500;
		word-wrap: break-word;
		overflow-wrap: break-word;
	}

	.routine-display.preview-mode .step-name-large {
		font-size: 2rem;
		font-weight: 600;
	}

	.step-controls {
		display: flex;
		gap: 0.25rem;
	}

	.control-btn {
		width: 32px;
		height: 32px;
		border: none;
		background: #95a5a6;
		color: white;
		border-radius: 4px;
		cursor: pointer;
		font-size: 1.2rem;
		transition: background 0.3s;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.control-btn:hover:not(:disabled) {
		background: #7f8c8d;
	}

	.control-btn:disabled {
		opacity: 0.3;
		cursor: not-allowed;
	}

	.control-btn.remove {
		background: #e74c3c;
	}

	.control-btn.remove:hover {
		background: #c0392b;
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

		.routine-display {
			page-break-inside: avoid;
		}

		.routine-step {
			page-break-inside: avoid;
			padding: 1.5rem;
			background: white;
			border: 3px solid #3498db;
		}

		.step-number {
			width: 60px;
			height: 60px;
			font-size: 1.8rem;
		}

		.step-icon-large {
			font-size: 4rem;
		}

		.step-name-large {
			font-size: 2rem;
			font-weight: 600;
		}
	}

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}

		.available-steps {
			grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
		}

		.routine-type-buttons {
			flex-direction: column;
		}

		.type-button {
			min-width: 100%;
		}

		.preview-actions {
			width: 100%;
			justify-content: stretch;
		}

		.preview-button,
		.print-button,
		.clear-button {
			flex: 1;
		}
	}
</style>
