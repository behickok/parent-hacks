<script>
	import { goto } from '$app/navigation';

	let activityType = $state('quiet');
	let childAge = $state(3);
	let availableMaterials = $state([]);
	let currentActivity = $state(null);

	const materials = [
		{ id: 'paper', name: 'Paper', icon: '📄' },
		{ id: 'crayons', name: 'Crayons', icon: '🖍️' },
		{ id: 'playdough', name: 'Play-Dough', icon: '🎨' },
		{ id: 'blocks', name: 'Blocks/Legos', icon: '🧱' },
		{ id: 'books', name: 'Books', icon: '📚' },
		{ id: 'puzzles', name: 'Puzzles', icon: '🧩' },
		{ id: 'blankets', name: 'Blankets', icon: '🛏️' },
		{ id: 'music', name: 'Music Player', icon: '🎵' },
		{ id: 'toys', name: 'Small Toys', icon: '🧸' },
		{ id: 'outdoor', name: 'Outdoor Space', icon: '🌳' }
	];

	const quietActivities = {
		'2-3': [
			{
				name: 'Color Sorting',
				materials: ['toys', 'blocks'],
				description: 'Sort toys or blocks by color into different containers.',
				duration: '10-15 minutes',
				setup: 'Provide 2-3 containers and a variety of colored items.'
			},
			{
				name: 'Sticker Play',
				materials: ['paper'],
				description: 'Let them place stickers on paper to create pictures.',
				duration: '10-20 minutes',
				setup: 'Give them a sheet of paper and a variety of stickers.'
			},
			{
				name: 'Board Books',
				materials: ['books'],
				description: 'Look at board books independently, turning pages and looking at pictures.',
				duration: '5-15 minutes',
				setup: 'Provide 3-5 favorite board books in a cozy spot.'
			},
			{
				name: 'Sensory Bin',
				materials: ['toys'],
				description: 'Explore a bin filled with rice, beans, or pasta with scoops and containers.',
				duration: '15-20 minutes',
				setup: 'Fill a large container with dry materials and add cups/spoons.'
			},
			{
				name: 'Simple Puzzles',
				materials: ['puzzles'],
				description: 'Work on chunky puzzles with large pieces.',
				duration: '10-15 minutes',
				setup: 'Offer 2-3 puzzles with 4-8 large pieces.'
			}
		],
		'4-5': [
			{
				name: 'Coloring Station',
				materials: ['paper', 'crayons'],
				description: 'Color in coloring books or draw freely on blank paper.',
				duration: '20-30 minutes',
				setup: 'Set up paper, crayons, and maybe coloring books at a table.'
			},
			{
				name: 'Play-Dough Creations',
				materials: ['playdough'],
				description: 'Create sculptures, animals, or food from play-dough.',
				duration: '20-30 minutes',
				setup: 'Provide play-dough and simple tools like cookie cutters.'
			},
			{
				name: 'Building Challenge',
				materials: ['blocks'],
				description: 'Build a tower, house, or vehicle with blocks or Legos.',
				duration: '20-30 minutes',
				setup: 'Give them a building challenge or let them free-build.'
			},
			{
				name: 'Audio Books',
				materials: ['music', 'books'],
				description: 'Listen to audiobooks or stories while looking at pictures.',
				duration: '15-30 minutes',
				setup: 'Set up audiobook or story podcast with headphones or speaker.'
			},
			{
				name: 'Pretend Play',
				materials: ['toys', 'blankets'],
				description: 'Play house, doctor, or restaurant with toys and props.',
				duration: '20-40 minutes',
				setup: 'Set up a play area with relevant toys and costumes.'
			},
			{
				name: 'Matching Games',
				materials: ['toys', 'paper'],
				description: 'Match pairs of items, pictures, or cards.',
				duration: '15-20 minutes',
				setup: 'Create or provide matching cards or objects.'
			}
		],
		'6+': [
			{
				name: 'Art Project',
				materials: ['paper', 'crayons'],
				description: 'Create a more complex art project like drawing, painting, or crafting.',
				duration: '30-45 minutes',
				setup: 'Provide art supplies and maybe a project idea or prompt.'
			},
			{
				name: 'Chapter Book Reading',
				materials: ['books'],
				description: 'Read a chapter book independently in a cozy spot.',
				duration: '20-40 minutes',
				setup: 'Have age-appropriate books available in a comfortable reading nook.'
			},
			{
				name: 'Complex Building',
				materials: ['blocks'],
				description: 'Build detailed structures following instructions or own design.',
				duration: '30-60 minutes',
				setup: 'Provide Legos, K\'nex, or other building materials with instructions if desired.'
			},
			{
				name: 'Journaling/Writing',
				materials: ['paper', 'crayons'],
				description: 'Write stories, draw comics, or keep a journal.',
				duration: '20-30 minutes',
				setup: 'Provide notebook or paper and writing tools.'
			},
			{
				name: 'Science Experiment',
				materials: ['paper'],
				description: 'Simple experiments like making slime, volcanoes, or crystals.',
				duration: '30-45 minutes',
				setup: 'Set up materials for a simple, safe experiment with instructions.'
			},
			{
				name: 'Puzzle Challenge',
				materials: ['puzzles'],
				description: 'Work on age-appropriate jigsaw puzzles.',
				duration: '30-60 minutes',
				setup: 'Provide puzzles with 50+ pieces appropriate to their skill level.'
			}
		]
	};

	const sensoryActivities = {
		calming: [
			{
				name: 'Deep Pressure Hug',
				type: 'Proprioceptive',
				description: 'Give a firm, long hug or have child squeeze under heavy cushions.',
				duration: '2-5 minutes',
				when: 'Child is overwhelmed, anxious, or overstimulated',
				icon: '🤗'
			},
			{
				name: 'Wall Pushes',
				type: 'Proprioceptive',
				description: 'Have child push against a wall with both hands, as if trying to move it.',
				duration: '3-5 minutes',
				when: 'Need to release energy while staying calm',
				icon: '💪'
			},
			{
				name: 'Slow Rocking',
				type: 'Vestibular',
				description: 'Rock slowly in a rocking chair or swing gently.',
				duration: '5-10 minutes',
				when: 'Child needs to calm down or regulate emotions',
				icon: '🪑'
			},
			{
				name: 'Deep Breathing',
				type: 'Proprioceptive',
				description: 'Breathe deeply together - in for 4, hold for 4, out for 4.',
				duration: '3-5 minutes',
				when: 'Anxiety, anger, or need to reset',
				icon: '💨'
			},
			{
				name: 'Heavy Work',
				type: 'Proprioceptive',
				description: 'Carry heavy books, push laundry basket, or help move furniture.',
				duration: '5-10 minutes',
				when: 'Child is restless or needs to calm down',
				icon: '📦'
			},
			{
				name: 'Gentle Music',
				type: 'Auditory',
				description: 'Listen to soft, calming music or nature sounds.',
				duration: '5-15 minutes',
				when: 'Bedtime, rest time, or after upset',
				icon: '🎵'
			},
			{
				name: 'Tight Squeezes',
				type: 'Proprioceptive',
				description: 'Squeeze stress ball, therapy putty, or play-dough firmly.',
				duration: '3-5 minutes',
				when: 'Fidgety, anxious, or frustrated',
				icon: '🎾'
			},
			{
				name: 'Weighted Lap Pad',
				type: 'Proprioceptive',
				description: 'Place a weighted lap pad or heavy blanket on child\'s lap.',
				duration: '10-20 minutes',
				when: 'During quiet time, homework, or to calm',
				icon: '🛏️'
			}
		],
		alerting: [
			{
				name: 'Jumping Jacks',
				type: 'Proprioceptive',
				description: 'Do 10-20 jumping jacks to wake up the body and brain.',
				duration: '2-3 minutes',
				when: 'Sluggish, tired, or needs energy boost',
				icon: '🤸'
			},
			{
				name: 'Dance Party',
				type: 'Vestibular',
				description: 'Put on upbeat music and dance energetically.',
				duration: '5-10 minutes',
				when: 'Low energy, before focusing tasks, or to wake up',
				icon: '💃'
			},
			{
				name: 'Animal Walks',
				type: 'Proprioceptive',
				description: 'Walk like a bear (on hands and feet), crab walk, or hop like a bunny.',
				duration: '3-5 minutes',
				when: 'Transition times or need physical input',
				icon: '🐻'
			},
			{
				name: 'Simon Says',
				type: 'Vestibular',
				description: 'Play Simon Says with active movements (jump, spin, reach high).',
				duration: '5-10 minutes',
				when: 'Need movement with some structure',
				icon: '👋'
			},
			{
				name: 'Obstacle Course',
				type: 'Proprioceptive/Vestibular',
				description: 'Set up simple obstacle course with cushions, chairs, tape on floor.',
				duration: '10-15 minutes',
				when: 'Indoor play, rainy days, or high energy',
				icon: '🏃'
			},
			{
				name: 'Freeze Dance',
				type: 'Vestibular',
				description: 'Dance to music, freeze when it stops.',
				duration: '5-10 minutes',
				when: 'Need movement plus listening/control practice',
				icon: '🎶'
			},
			{
				name: 'Spinning',
				type: 'Vestibular',
				description: 'Spin in an office chair or do slow twirls (monitor for dizziness).',
				duration: '2-5 minutes',
				when: 'Seeking sensory input, limited space',
				icon: '🔄'
			},
			{
				name: 'Crunchy Snacks',
				type: 'Oral Motor',
				description: 'Provide crunchy snacks like carrots, apples, or pretzels.',
				duration: '5 minutes',
				when: 'Need oral input to wake up or focus',
				icon: '🥕'
			}
		]
	};

	function getAgeGroup(age) {
		if (age <= 3) return '2-3';
		if (age <= 5) return '4-5';
		return '6+';
	}

	function toggleMaterial(materialId) {
		if (availableMaterials.includes(materialId)) {
			availableMaterials = availableMaterials.filter((m) => m !== materialId);
		} else {
			availableMaterials = [...availableMaterials, materialId];
		}
	}

	function generateActivity() {
		if (activityType === 'quiet') {
			const ageGroup = getAgeGroup(childAge);
			let possibleActivities = quietActivities[ageGroup];

			// Filter by available materials if any selected
			if (availableMaterials.length > 0) {
				possibleActivities = possibleActivities.filter((activity) =>
					activity.materials.some((mat) => availableMaterials.includes(mat))
				);
			}

			if (possibleActivities.length === 0) {
				possibleActivities = quietActivities[ageGroup]; // Fallback to all activities
			}

			const randomIndex = Math.floor(Math.random() * possibleActivities.length);
			currentActivity = possibleActivities[randomIndex];
		} else {
			// Sensory activities
			const sensoryType = activityType; // 'calming' or 'alerting'
			const possibleActivities = sensoryActivities[sensoryType];
			const randomIndex = Math.floor(Math.random() * possibleActivities.length);
			currentActivity = possibleActivities[randomIndex];
		}
	}

	function reset() {
		currentActivity = null;
	}
</script>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>Activity Generator</h1>
		<div style="width: 100px;"></div>
	</header>

	<div class="intro">
		<p>Generate activity ideas for quiet time, sensory breaks, or screen-free play!</p>
	</div>

	<div class="settings-section">
		<div class="setting">
			<label>Activity Type:</label>
			<div class="type-buttons">
				<button
					class="type-button"
					class:active={activityType === 'quiet'}
					onclick={() => { activityType = 'quiet'; reset(); }}
				>
					🤫 Quiet Time Activities
				</button>
				<button
					class="type-button"
					class:active={activityType === 'calming'}
					onclick={() => { activityType = 'calming'; reset(); }}
				>
					😌 Calming Sensory Breaks
				</button>
				<button
					class="type-button"
					class:active={activityType === 'alerting'}
					onclick={() => { activityType = 'alerting'; reset(); }}
				>
					⚡ Alerting Sensory Breaks
				</button>
			</div>
		</div>

		{#if activityType === 'quiet'}
			<div class="setting">
				<label>Child's Age: {childAge} years old</label>
				<input type="range" min="2" max="10" bind:value={childAge} onchange={reset} />
			</div>

			<div class="setting">
				<label>Available Materials (optional - select what you have):</label>
				<div class="materials-grid">
					{#each materials as material}
						<button
							class="material-chip"
							class:selected={availableMaterials.includes(material.id)}
							onclick={() => { toggleMaterial(material.id); reset(); }}
						>
							<span class="material-icon">{material.icon}</span>
							<span class="material-name">{material.name}</span>
						</button>
					{/each}
				</div>
			</div>
		{/if}
	</div>

	<div class="generate-section">
		<button class="generate-button" onclick={generateActivity}>
			🎲 Generate Activity Idea
		</button>
	</div>

	{#if currentActivity}
		<div class="activity-result">
			{#if activityType === 'quiet'}
				<div class="activity-header">
					<h2>{currentActivity.name}</h2>
					<div class="duration">{currentActivity.duration}</div>
				</div>

				<div class="activity-description">
					<p>{currentActivity.description}</p>
				</div>

				<div class="activity-setup">
					<h3>Setup:</h3>
					<p>{currentActivity.setup}</p>
				</div>

				<div class="materials-needed">
					<h3>Materials:</h3>
					<div class="material-tags">
						{#each currentActivity.materials as materialId}
							{@const mat = materials.find((m) => m.id === materialId)}
							{#if mat}
								<span class="material-tag">
									{mat.icon} {mat.name}
								</span>
							{/if}
						{/each}
					</div>
				</div>
			{:else}
				<div class="sensory-header">
					<div class="sensory-icon">{currentActivity.icon}</div>
					<h2>{currentActivity.name}</h2>
					<div class="sensory-type">{currentActivity.type} Input</div>
				</div>

				<div class="activity-description">
					<p>{currentActivity.description}</p>
				</div>

				<div class="sensory-details">
					<div class="detail-item">
						<strong>Duration:</strong> {currentActivity.duration}
					</div>
					<div class="detail-item">
						<strong>When to use:</strong> {currentActivity.when}
					</div>
				</div>
			{/if}

			<button class="another-button" onclick={generateActivity}>
				Generate Another Activity
			</button>
		</div>

		<div class="tips-box">
			<h3>💡 Tips:</h3>
			{#if activityType === 'quiet'}
				<ul>
					<li>Set a timer so child knows how long quiet time lasts</li>
					<li>Rotate activities to keep them interesting</li>
					<li>Start with shorter periods and gradually increase</li>
					<li>Create a designated quiet time space</li>
					<li>Praise independent play efforts</li>
				</ul>
			{:else if activityType === 'calming'}
				<ul>
					<li>Use calming activities before transitions, bedtime, or after upset</li>
					<li>Model calm yourself - your regulation helps them regulate</li>
					<li>Make it a routine part of the day, not just for meltdowns</li>
					<li>Let child choose which calming activity feels right</li>
					<li>Combine multiple activities for best effect</li>
				</ul>
			{:else}
				<ul>
					<li>Use alerting activities in the morning or when sluggish</li>
					<li>Great before homework or tasks requiring focus</li>
					<li>Monitor energy levels - stop if child becomes overstimulated</li>
					<li>Follow with a calming activity if needed</li>
					<li>Make it fun! Movement should be enjoyable</li>
				</ul>
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

	.settings-section {
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

	.type-buttons {
		display: flex;
		gap: 1rem;
		flex-wrap: wrap;
	}

	.type-button {
		flex: 1;
		min-width: 200px;
		padding: 1rem;
		border: 2px solid #e0e0e0;
		background: white;
		border-radius: 8px;
		cursor: pointer;
		font-size: 1rem;
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

	input[type='range'] {
		width: 100%;
	}

	.materials-grid {
		display: grid;
		grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
		gap: 0.75rem;
		margin-top: 0.5rem;
	}

	.material-chip {
		background: white;
		border: 2px solid #e0e0e0;
		border-radius: 20px;
		padding: 0.5rem 1rem;
		cursor: pointer;
		transition: all 0.3s;
		display: flex;
		align-items: center;
		gap: 0.5rem;
		font-size: 0.9rem;
	}

	.material-chip:hover {
		border-color: #3498db;
	}

	.material-chip.selected {
		border-color: #27ae60;
		background: #e8f8f5;
	}

	.material-icon {
		font-size: 1.2rem;
	}

	.material-name {
		color: #2c3e50;
	}

	.generate-section {
		text-align: center;
		margin-bottom: 2rem;
	}

	.generate-button {
		background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
		color: white;
		border: none;
		padding: 1.5rem 3rem;
		border-radius: 12px;
		cursor: pointer;
		font-size: 1.3rem;
		font-weight: 600;
		transition: all 0.3s;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
	}

	.generate-button:hover {
		transform: translateY(-2px);
		box-shadow: 0 6px 16px rgba(0, 0, 0, 0.3);
	}

	.activity-result {
		background: white;
		padding: 2rem;
		border-radius: 12px;
		margin-bottom: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.activity-header {
		text-align: center;
		margin-bottom: 1.5rem;
	}

	.activity-header h2 {
		margin: 0 0 0.5rem 0;
		color: #2c3e50;
		font-size: 2rem;
	}

	.duration {
		color: #7f8c8d;
		font-size: 1.1rem;
		font-weight: 600;
	}

	.sensory-header {
		text-align: center;
		margin-bottom: 1.5rem;
	}

	.sensory-icon {
		font-size: 5rem;
		margin-bottom: 1rem;
	}

	.sensory-header h2 {
		margin: 0 0 0.5rem 0;
		color: #2c3e50;
		font-size: 2rem;
	}

	.sensory-type {
		color: #7f8c8d;
		font-size: 1.1rem;
		font-weight: 600;
	}

	.activity-description {
		font-size: 1.2rem;
		line-height: 1.6;
		color: #2c3e50;
		margin-bottom: 1.5rem;
		text-align: center;
		word-wrap: break-word;
		overflow-wrap: break-word;
	}

	.activity-setup,
	.materials-needed,
	.sensory-details {
		margin-bottom: 1.5rem;
	}

	.activity-setup h3,
	.materials-needed h3 {
		color: #2c3e50;
		margin-bottom: 0.5rem;
	}

	.activity-setup p {
		color: #2c3e50;
		line-height: 1.6;
		word-wrap: break-word;
		overflow-wrap: break-word;
	}

	.material-tags {
		display: flex;
		flex-wrap: wrap;
		gap: 0.5rem;
	}

	.material-tag {
		background: #e3f2fd;
		color: #2c3e50;
		padding: 0.5rem 1rem;
		border-radius: 20px;
		font-size: 0.9rem;
		border: 2px solid #3498db;
	}

	.sensory-details {
		background: #f8f9fa;
		padding: 1rem;
		border-radius: 8px;
	}

	.detail-item {
		color: #2c3e50;
		line-height: 1.8;
		margin-bottom: 0.5rem;
	}

	.detail-item:last-child {
		margin-bottom: 0;
	}

	.another-button {
		width: 100%;
		background: #3498db;
		color: white;
		border: none;
		padding: 1rem;
		border-radius: 8px;
		cursor: pointer;
		font-size: 1.1rem;
		font-weight: 600;
		transition: all 0.3s;
		margin-top: 1rem;
	}

	.another-button:hover {
		background: #2980b9;
		transform: translateY(-2px);
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

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}

		.type-buttons {
			flex-direction: column;
		}

		.type-button {
			min-width: 100%;
		}

		.materials-grid {
			grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
		}
	}
</style>
