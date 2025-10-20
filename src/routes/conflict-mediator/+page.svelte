<script>
	import { goto } from '$app/navigation';

	let currentStep = $state(0);
	let conflictType = $state(null);
	let child1Name = $state('Child 1');
	let child2Name = $state('Child 2');
	let mediationNotes = $state([]);

	const conflictTypes = {
		toy: {
			title: 'Fighting Over a Toy',
			icon: '🧸',
			color: '#e74c3c',
			steps: [
				{
					question: 'Who had the toy first?',
					options: [
						{ label: '{child1}', action: 'child1-first' },
						{ label: '{child2}', action: 'child2-first' },
						{ label: "Don't know / Both at same time", action: 'unknown' }
					]
				},
				{
					question: 'How long has {firstChild} been playing with it?',
					options: [
						{ label: 'Just picked it up (< 1 minute)', action: 'just-started' },
						{ label: 'A few minutes (1-5 minutes)', action: 'short-time' },
						{ label: 'A while (5+ minutes)', action: 'long-time' }
					]
				}
			],
			resolutions: {
				'child1-first-long-time': {
					title: '{child1} Had It First & Has Been Playing A While',
					script: `"{child1}, you've been playing with the {item} for a while now. {child2} would like a turn. Let's set a timer for 2 more minutes, then it will be {child2}'s turn. {child2}, I know waiting is hard. While you wait, would you like to [suggest alternative activity]?"`,
					tips: [
						'Acknowledge both children\'s feelings',
						'Use a visual timer',
						'Offer the waiting child an alternative',
						'Follow through on the time limit'
					]
				},
				'child1-first-short-time': {
					title: '{child1} Just Started Playing',
					script: `"{child1} just started playing with the {item}. {child2}, {child1} gets to finish their turn first. Let's set a timer for 5 minutes. Would you like to play with [similar toy] while you wait, or should we find something else fun?"`,
					tips: [
						'Protect the first child\'s right to play',
						'Give concrete wait time',
						'Help waiting child find alternative',
						'Praise patience when timer goes off'
					]
				},
				'child2-first-long-time': {
					title: '{child2} Had It First & Has Been Playing A While',
					script: `"{child2}, you've been playing with the {item} for a while now. {child1} would like a turn. Let's set a timer for 2 more minutes, then it will be {child1}'s turn. {child1}, I know waiting is hard. While you wait, would you like to [suggest alternative activity]?"`,
					tips: [
						'Acknowledge both children\'s feelings',
						'Use a visual timer',
						'Offer the waiting child an alternative',
						'Follow through on the time limit'
					]
				},
				'child2-first-short-time': {
					title: '{child2} Just Started Playing',
					script: `"{child2} just started playing with the {item}. {child1}, {child2} gets to finish their turn first. Let's set a timer for 5 minutes. Would you like to play with [similar toy] while you wait, or should we find something else fun?"`,
					tips: [
						'Protect the first child\'s right to play',
						'Give concrete wait time',
						'Help waiting child find alternative',
						'Praise patience when timer goes off'
					]
				},
				unknown: {
					title: 'Both Want It / Unknown Who Had It First',
					script: `"You both want the {item} and that's causing a problem. Since we don't know who had it first, I'm going to put the {item} away for now. You can both try again later when you're ready to take turns. Right now, let's find something else you can each play with."`,
					tips: [
						'Remove the item temporarily',
						'Don\'t try to determine who\'s "right"',
						'Help both children find new activities',
						'Bring item back later with clear turn-taking rules'
					]
				}
			}
		},
		turn: {
			title: 'Not Taking Turns',
			icon: '🔄',
			color: '#3498db',
			steps: [
				{
					question: 'What are they taking turns with?',
					options: [
						{ label: 'A toy or game', action: 'toy' },
						{ label: 'Screen time / device', action: 'screen' },
						{ label: 'Parent attention', action: 'attention' },
						{ label: 'Activity or privilege', action: 'activity' }
					]
				}
			],
			resolutions: {
				toy: {
					title: 'Turn-Taking with Toy/Game',
					script: `"You both want to use the {item}. We're going to take turns. I'm setting a timer for 5 minutes. {child1}, you go first. {child2}, you're next. When the timer beeps, we'll switch. {child2}, while you're waiting, you can [alternative activity]."`,
					tips: [
						'Use a visible/audible timer',
						'Make turns equal length',
						'Stick to the timer strictly',
						'Alternate who goes first next time',
						'Praise good waiting and sharing'
					]
				},
				screen: {
					title: 'Turn-Taking with Screen Time',
					script: `"You both want screen time. {child1} will have 15 minutes first, then {child2} will have 15 minutes. I'm setting the timer now. When it beeps, we switch - no arguing. If there's arguing, screen time ends for everyone."`,
					tips: [
						'Set clear time limits before starting',
						'Use parental controls if needed',
						'Have consequence ready for non-compliance',
						'Consider giving them same total time on different devices',
						'Rotate who goes first'
					]
				},
				attention: {
					title: 'Competing for Parent Attention',
					script: `"I can see you both need me right now. {child1}, I'm going to help you first for 5 minutes, then {child2}, you'll have my full attention. {child2}, can you be patient for 5 minutes? You can [quiet activity] while you wait. Then it's your turn and {child1} will wait."`,
					tips: [
						'Acknowledge both children\'s needs',
						'Give specific wait times',
						'Follow through completely',
						'Try to give each child some one-on-one time daily',
						'Teach them to say "Excuse me" politely'
					]
				},
				activity: {
					title: 'Turn-Taking with Activity',
					script: `"Only one person can [activity] at a time. {child1} will go first for [time], then {child2}. I'm setting a timer so it's fair. Whoever goes second will get to go first next time we do this."`,
					tips: [
						'Keep a mental note of who went first',
						'Alternate first-turn privilege',
						'Make wait times reasonable',
						'Offer something else during wait',
						'Celebrate good sportsmanship'
					]
				}
			}
		},
		hitting: {
			title: 'Physical Aggression (Hitting/Pushing)',
			icon: '✋',
			color: '#e67e22',
			steps: [
				{
					question: 'Did you see what happened?',
					options: [
						{ label: 'Yes, I saw who hit/pushed first', action: 'saw' },
						{ label: 'No, I heard the conflict but didn\'t see', action: 'didnt-see' }
					]
				}
			],
			resolutions: {
				saw: {
					title: 'You Witnessed the Hitting',
					script: `[To aggressor]: "{child}, I won't let you hit. Hitting hurts. I can see you're [angry/frustrated/upset], but bodies are not for hurting. Use your words to tell {other child} how you feel." [To victim]: "Are you okay? That hurt. {child} is learning to use words instead of hitting." [Separation]: "You both need some space. {child}, come with me for a minute."`,
					tips: [
						'Stop the behavior immediately',
						'Name the emotion behind the hitting',
						'Teach the words they could use instead',
						'Comfort the hurt child',
						'Separate them briefly to cool down',
						'Address the underlying conflict after everyone is calm',
						'Never hit back or punish with hitting'
					]
				},
				'didnt-see': {
					title: 'You Didn\'t See Who Started It',
					script: `"I can see someone got hurt and you're both upset. I didn't see what happened, so I can't say who was right or wrong. What I know is: hitting is not okay. Both of you need to use words. Let's take some deep breaths and calm down, then we'll talk about what to do differently next time."`,
					tips: [
						'Don\'t try to figure out who\'s "guilty"',
						'Focus on the rule: no hitting',
						'Separate them to calm down',
						'Address problem-solving skills',
						'Watch more closely next time',
						'Teach both children alternatives to hitting'
					]
				}
			}
		},
		tattling: {
			title: 'Tattling / Reporting',
			icon: '🗣️',
			color: '#9b59b6',
			steps: [
				{
					question: 'Is someone hurt or in danger?',
					options: [
						{ label: 'Yes, someone is hurt or unsafe', action: 'safety' },
						{ label: 'No, they\'re trying to get sibling in trouble', action: 'tattling' }
					]
				}
			],
			resolutions: {
				safety: {
					title: 'Safety Concern - Good Reporting',
					script: `"Thank you for telling me. That was the right thing to do because someone could get hurt. Let me go check." [Address the safety issue, then return]: "You did a good job telling me about something important. I'm glad you told me."`,
					tips: [
						'Praise them for reporting safety issues',
						'Address the safety concern immediately',
						'Teach the difference between tattling and reporting',
						'Reinforce that safety issues should always be reported'
					]
				},
				tattling: {
					title: 'Tattling to Get Someone in Trouble',
					script: `"It sounds like you're trying to get {sibling} in trouble. Is someone hurt? No? Then this is something you two can work out together. You can tell {sibling} how you feel, or you can choose to play somewhere else. I'll only step in if someone is being hurt or if someone asks for help politely."`,
					tips: [
						'Don\'t engage with petty tattling',
						'Teach kids to solve minor conflicts themselves',
						'Ask: "Is someone hurt? Is someone being unsafe?"',
						'Redirect them to talk to each other',
						'Only intervene for real problems',
						'Praise problem-solving without adult help'
					]
				}
			}
		}
	};

	let resolutionData = $state({});

	function selectConflict(type) {
		conflictType = type;
		currentStep = 0;
		mediationNotes = [];
		resolutionData = {};
	}

	function selectOption(action) {
		mediationNotes.push(action);
		resolutionData[`step${currentStep}`] = action;

		const conflict = conflictTypes[conflictType];

		if (currentStep < conflict.steps.length - 1) {
			currentStep++;
		} else {
			// Determine resolution
			showResolution();
		}
	}

	function showResolution() {
		currentStep = 'resolution';
	}

	function getResolution() {
		const conflict = conflictTypes[conflictType];
		let resolutionKey = mediationNotes.join('-');

		// Find matching resolution or use a default
		if (conflict.resolutions[resolutionKey]) {
			return conflict.resolutions[resolutionKey];
		}

		// Try partial matches
		for (let key in conflict.resolutions) {
			if (resolutionKey.includes(key) || key.includes(resolutionKey)) {
				return conflict.resolutions[key];
			}
		}

		// Default to first resolution
		return Object.values(conflict.resolutions)[0];
	}

	function replacePlaceholders(text) {
		return text
			.replace(/{child1}/g, child1Name)
			.replace(/{child2}/g, child2Name)
			.replace(/{child}/g, child1Name)
			.replace(/{other child}/g, child2Name)
			.replace(/{firstChild}/g, resolutionData.step0 === 'child1-first' ? child1Name : child2Name)
			.replace(/{item}/g, 'toy')
			.replace(/{sibling}/g, child2Name);
	}

	function reset() {
		conflictType = null;
		currentStep = 0;
		mediationNotes = [];
		resolutionData = {};
	}

	function copyScript(text) {
		navigator.clipboard.writeText(replacePlaceholders(text));
		alert('Script copied to clipboard!');
	}
</script>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>Sibling Conflict Mediator</h1>
		<div style="width: 100px;"></div>
	</header>

	<div class="intro">
		<p>Step-by-step guidance for resolving common sibling conflicts fairly and calmly.</p>
	</div>

	{#if !conflictType}
		<div class="names-section">
			<h2>Children's Names (optional)</h2>
			<div class="name-inputs">
				<input type="text" bind:value={child1Name} placeholder="First child's name" />
				<input type="text" bind:value={child2Name} placeholder="Second child's name" />
			</div>
		</div>

		<div class="conflict-types">
			<h2>What type of conflict is happening?</h2>
			<div class="types-grid">
				{#each Object.entries(conflictTypes) as [key, type]}
					<button
						class="conflict-card"
						style="border-color: {type.color}"
						onclick={() => selectConflict(key)}
					>
						<div class="conflict-icon">{type.icon}</div>
						<h3>{type.title}</h3>
					</button>
				{/each}
			</div>
		</div>
	{:else if currentStep !== 'resolution'}
		<div class="mediation-section">
			<button class="breadcrumb-button" onclick={reset}>← Start Over</button>

			<div class="progress">
				<div class="progress-text">
					Question {currentStep + 1} of {conflictTypes[conflictType].steps.length}
				</div>
				<div class="progress-bar-container">
					<div
						class="progress-bar"
						style="width: {((currentStep + 1) / conflictTypes[conflictType].steps.length) * 100}%"
					></div>
				</div>
			</div>

			<div class="question-card">
				<h2>{replacePlaceholders(conflictTypes[conflictType].steps[currentStep].question)}</h2>
				<div class="options">
					{#each conflictTypes[conflictType].steps[currentStep].options as option}
						<button class="option-button" onclick={() => selectOption(option.action)}>
							{replacePlaceholders(option.label)}
						</button>
					{/each}
				</div>
			</div>
		</div>
	{:else}
		<div class="resolution-section">
			<button class="breadcrumb-button" onclick={reset}>← Handle Another Conflict</button>

			{#if getResolution()}
				{@const resolution = getResolution()}
				<div class="resolution-header" style="background: {conflictTypes[conflictType].color}">
					<div class="resolution-icon">{conflictTypes[conflictType].icon}</div>
					<h2>{replacePlaceholders(resolution.title)}</h2>
				</div>

				<div class="script-box">
					<h3>What to Say:</h3>
					<div class="script-text">{replacePlaceholders(resolution.script)}</div>
					<button class="copy-button" onclick={() => copyScript(resolution.script)}>
						📋 Copy Script
					</button>
				</div>

				<div class="tips-section">
					<h3>How to Handle This:</h3>
					<ul>
						{#each resolution.tips as tip}
							<li>{tip}</li>
						{/each}
					</ul>
				</div>

				<div class="general-tips">
					<h3>💡 General Mediation Tips:</h3>
					<ul>
						<li>Stay calm and neutral - you're the referee, not taking sides</li>
						<li>Get down to their eye level</li>
						<li>Acknowledge both children's feelings</li>
						<li>Separate them if emotions are too high</li>
						<li>Teach the skills now, but follow through later</li>
						<li>Praise any attempt at peaceful resolution</li>
					</ul>
				</div>
			{/if}
		</div>
	{/if}
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
	}

	.names-section {
		background: white;
		padding: 1.5rem;
		border-radius: 12px;
		margin-bottom: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.names-section h2 {
		margin-top: 0;
		color: #2c3e50;
		font-size: 1.3rem;
	}

	.name-inputs {
		display: flex;
		gap: 1rem;
	}

	.name-inputs input {
		flex: 1;
		padding: 0.75rem;
		border: 2px solid #e0e0e0;
		border-radius: 8px;
		font-size: 1rem;
	}

	.conflict-types {
		background: white;
		padding: 1.5rem;
		border-radius: 12px;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.conflict-types h2 {
		margin-top: 0;
		color: #2c3e50;
	}

	.types-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
		gap: 1.5rem;
	}

	.conflict-card {
		background: white;
		border: 3px solid;
		border-radius: 12px;
		padding: 2rem;
		cursor: pointer;
		transition: all 0.3s;
		text-align: center;
	}

	.conflict-card:hover {
		transform: translateY(-4px);
		box-shadow: 0 6px 16px rgba(0, 0, 0, 0.15);
	}

	.conflict-icon {
		font-size: 4rem;
		margin-bottom: 1rem;
	}

	.conflict-card h3 {
		margin: 0;
		color: #2c3e50;
		font-size: 1.1rem;
	}

	.breadcrumb-button {
		background: #ecf0f1;
		border: none;
		padding: 0.5rem 1rem;
		border-radius: 6px;
		cursor: pointer;
		color: #2c3e50;
		font-size: 0.95rem;
		transition: background 0.3s;
		margin-bottom: 1.5rem;
	}

	.breadcrumb-button:hover {
		background: #bdc3c7;
	}

	.progress {
		margin-bottom: 2rem;
	}

	.progress-text {
		text-align: center;
		color: #7f8c8d;
		margin-bottom: 0.5rem;
		font-weight: 600;
	}

	.progress-bar-container {
		width: 100%;
		height: 12px;
		background: #e0e0e0;
		border-radius: 6px;
		overflow: hidden;
	}

	.progress-bar {
		height: 100%;
		background: #3498db;
		transition: width 0.3s ease;
	}

	.question-card {
		background: white;
		padding: 2rem;
		border-radius: 12px;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.question-card h2 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 2rem;
		text-align: center;
	}

	.options {
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}

	.option-button {
		background: white;
		border: 2px solid #3498db;
		padding: 1.5rem;
		border-radius: 8px;
		cursor: pointer;
		font-size: 1.1rem;
		color: #2c3e50;
		transition: all 0.3s;
		text-align: left;
	}

	.option-button:hover {
		background: #e3f2fd;
		transform: translateX(4px);
	}

	.resolution-header {
		color: white;
		padding: 2rem;
		border-radius: 12px;
		text-align: center;
		margin-bottom: 2rem;
	}

	.resolution-icon {
		font-size: 4rem;
		margin-bottom: 1rem;
	}

	.resolution-header h2 {
		margin: 0;
		font-size: 1.8rem;
	}

	.script-box {
		background: #e8f4f8;
		border-left: 4px solid #3498db;
		padding: 1.5rem;
		border-radius: 8px;
		margin-bottom: 2rem;
	}

	.script-box h3 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 1rem;
	}

	.script-text {
		font-size: 1.05rem;
		line-height: 1.8;
		color: #2c3e50;
		font-style: italic;
		margin-bottom: 1rem;
		white-space: pre-line;
		word-wrap: break-word;
		overflow-wrap: break-word;
	}

	.copy-button {
		background: #3498db;
		color: white;
		border: none;
		padding: 0.5rem 1rem;
		border-radius: 6px;
		cursor: pointer;
		font-size: 0.9rem;
		transition: background 0.3s;
	}

	.copy-button:hover {
		background: #2980b9;
	}

	.tips-section {
		background: #fff9e6;
		border-left: 4px solid #f39c12;
		padding: 1.5rem;
		border-radius: 8px;
		margin-bottom: 2rem;
	}

	.tips-section h3 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 1rem;
	}

	.tips-section ul {
		margin: 0;
		padding-left: 1.5rem;
	}

	.tips-section li {
		color: #2c3e50;
		line-height: 1.8;
		margin-bottom: 0.5rem;
	}

	.general-tips {
		background: #e8f8f5;
		border-left: 4px solid #27ae60;
		padding: 1.5rem;
		border-radius: 8px;
	}

	.general-tips h3 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 1rem;
	}

	.general-tips ul {
		margin: 0;
		padding-left: 1.5rem;
	}

	.general-tips li {
		color: #2c3e50;
		line-height: 1.8;
		margin-bottom: 0.5rem;
	}

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}

		.name-inputs {
			flex-direction: column;
		}

		.types-grid {
			grid-template-columns: 1fr;
		}
	}
</style>
