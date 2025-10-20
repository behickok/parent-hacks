<script>
	import { goto } from '$app/navigation';

	let selectedEmotion = $state(null);
	let selectedIntensity = $state(null);
	let showDetails = $state(false);

	const emotions = {
		happy: {
			name: 'Happy',
			color: '#f1c40f',
			emoji: '😊',
			intensities: {
				mild: ['Content', 'Pleased', 'Satisfied', 'Calm'],
				medium: ['Happy', 'Cheerful', 'Joyful', 'Glad'],
				strong: ['Excited', 'Thrilled', 'Overjoyed', 'Ecstatic']
			},
			activities: [
				'Share what made you happy with someone',
				'Draw or paint your happy feeling',
				'Dance to your favorite song',
				'Give someone a hug'
			],
			parentTip: 'Acknowledge and celebrate happy feelings! Help your child notice what brings them joy and create opportunities for more positive experiences.'
		},
		sad: {
			name: 'Sad',
			color: '#3498db',
			emoji: '😢',
			intensities: {
				mild: ['Disappointed', 'Down', 'Blue', 'Gloomy'],
				medium: ['Sad', 'Unhappy', 'Sorrowful', 'Upset'],
				strong: ['Heartbroken', 'Devastated', 'Despairing', 'Grief-stricken']
			},
			activities: [
				'Talk about what\'s making you sad',
				'Have a gentle cry - it\'s okay!',
				'Snuggle with a favorite stuffed animal',
				'Look at happy photos or memories'
			],
			parentTip: 'Sadness is a valid emotion. Sit with your child, validate their feelings, and offer comfort. Avoid trying to "fix" it immediately - sometimes they just need to feel sad for a while.'
		},
		angry: {
			name: 'Angry',
			color: '#e74c3c',
			emoji: '😠',
			intensities: {
				mild: ['Annoyed', 'Irritated', 'Bothered', 'Frustrated'],
				medium: ['Angry', 'Mad', 'Cross', 'Upset'],
				strong: ['Furious', 'Enraged', 'Outraged', 'Livid']
			},
			activities: [
				'Take 5 deep belly breaths',
				'Stomp your feet or jump',
				'Squeeze a stress ball or pillow',
				'Draw angry scribbles on paper'
			],
			parentTip: 'Anger is okay, but we need safe ways to express it. Help your child identify what triggered the anger and teach them healthy outlets like movement or talking about it.'
		},
		scared: {
			name: 'Scared',
			color: '#9b59b6',
			emoji: '😨',
			intensities: {
				mild: ['Uneasy', 'Nervous', 'Worried', 'Uncertain'],
				medium: ['Scared', 'Afraid', 'Frightened', 'Anxious'],
				strong: ['Terrified', 'Panicked', 'Horrified', 'Petrified']
			},
			activities: [
				'Talk about what feels scary',
				'Hold someone\'s hand',
				'Breathe slowly with a trusted adult',
				'Think of something that makes you feel brave'
			],
			parentTip: 'Never dismiss a child\'s fears, even if they seem irrational. Validate their feelings, offer comfort and reassurance, and help them feel safe.'
		},
		surprised: {
			name: 'Surprised',
			color: '#e67e22',
			emoji: '😲',
			intensities: {
				mild: ['Curious', 'Interested', 'Intrigued', 'Puzzled'],
				medium: ['Surprised', 'Amazed', 'Astonished', 'Startled'],
				strong: ['Shocked', 'Astounded', 'Stunned', 'Flabbergasted']
			},
			activities: [
				'Talk about what surprised you',
				'Draw what you didn\'t expect',
				'Tell the story to someone',
				'Take a moment to process what happened'
			],
			parentTip: 'Surprise can be positive or negative. Help your child process unexpected events and talk through what they\'re feeling about the surprise.'
		},
		calm: {
			name: 'Calm',
			color: '#27ae60',
			emoji: '😌',
			intensities: {
				mild: ['Okay', 'Fine', 'Neutral', 'Balanced'],
				medium: ['Calm', 'Peaceful', 'Relaxed', 'Tranquil'],
				strong: ['Serene', 'Blissful', 'Zen', 'At peace']
			},
			activities: [
				'Enjoy the peaceful feeling',
				'Do some gentle stretching',
				'Listen to calm music',
				'Practice mindful breathing'
			],
			parentTip: 'Calm moments are wonderful opportunities to connect. Help your child notice and appreciate when they feel peaceful and regulated.'
		}
	};

	function selectEmotionCard(emotionKey) {
		selectedEmotion = emotionKey;
		selectedIntensity = null;
		showDetails = false;
	}

	function selectIntensityLevel(level) {
		selectedIntensity = level;
		showDetails = true;
	}

	function reset() {
		selectedEmotion = null;
		selectedIntensity = null;
		showDetails = false;
	}
</script>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>Feelings Wheel</h1>
		<div style="width: 100px;"></div>
	</header>

	<div class="intro">
		<p>How are you feeling? Click on an emotion to explore it more.</p>
	</div>

	{#if !selectedEmotion}
		<div class="emotions-grid">
			{#each Object.entries(emotions) as [key, emotion]}
				<button
					class="emotion-card"
					style="background: {emotion.color};"
					onclick={() => selectEmotionCard(key)}
				>
					<div class="emotion-emoji">{emotion.emoji}</div>
					<div class="emotion-name">{emotion.name}</div>
				</button>
			{/each}
		</div>

		<div class="tip-box">
			<h3>💡 For Parents:</h3>
			<p>
				All feelings are valid and important. Help your child build emotional vocabulary by naming
				feelings together. The more words they have for emotions, the better they can understand
				and express themselves.
			</p>
		</div>
	{:else if !showDetails}
		<div class="intensity-section">
			<button class="breadcrumb-button" onclick={reset}>← Back to all feelings</button>

			<div class="selected-emotion-header" style="background: {emotions[selectedEmotion].color};">
				<div class="big-emoji">{emotions[selectedEmotion].emoji}</div>
				<h2>I feel {emotions[selectedEmotion].name}</h2>
			</div>

			<div class="intensity-prompt">
				<p>How {emotions[selectedEmotion].name.toLowerCase()} do you feel?</p>
			</div>

			<div class="intensity-grid">
				<button class="intensity-card" onclick={() => selectIntensityLevel('mild')}>
					<h3>A Little</h3>
					<div class="intensity-words">
						{#each emotions[selectedEmotion].intensities.mild as word}
							<span class="word-badge">{word}</span>
						{/each}
					</div>
				</button>

				<button class="intensity-card" onclick={() => selectIntensityLevel('medium')}>
					<h3>Medium</h3>
					<div class="intensity-words">
						{#each emotions[selectedEmotion].intensities.medium as word}
							<span class="word-badge">{word}</span>
						{/each}
					</div>
				</button>

				<button class="intensity-card" onclick={() => selectIntensityLevel('strong')}>
					<h3>A Lot</h3>
					<div class="intensity-words">
						{#each emotions[selectedEmotion].intensities.strong as word}
							<span class="word-badge">{word}</span>
						{/each}
					</div>
				</button>
			</div>
		</div>
	{:else}
		<div class="details-section">
			<button class="breadcrumb-button" onclick={() => (showDetails = false)}>
				← Back to intensity
			</button>

			<div class="feeling-summary" style="border-color: {emotions[selectedEmotion].color};">
				<div class="summary-emoji">{emotions[selectedEmotion].emoji}</div>
				<div class="summary-text">
					<h2>I feel {emotions[selectedEmotion].name}</h2>
					<p class="intensity-level">
						{selectedIntensity === 'mild' ? 'A Little' : selectedIntensity === 'medium' ? 'Medium' : 'A Lot'}
					</p>
					<div class="chosen-words">
						{#each emotions[selectedEmotion].intensities[selectedIntensity] as word}
							<span class="word" style="background: {emotions[selectedEmotion].color};">{word}</span>
						{/each}
					</div>
				</div>
			</div>

			<div class="activities-box">
				<h3>Things you can do:</h3>
				<ul>
					{#each emotions[selectedEmotion].activities as activity}
						<li>{activity}</li>
					{/each}
				</ul>
			</div>

			<div class="parent-tip-box" style="border-left-color: {emotions[selectedEmotion].color};">
				<h3>💡 For Parents:</h3>
				<p>{emotions[selectedEmotion].parentTip}</p>
			</div>

			<div class="action-buttons">
				<button class="reset-button" onclick={reset}>Choose a Different Feeling</button>
			</div>
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
		font-size: 1.3rem;
		color: #2c3e50;
		margin-bottom: 2rem;
		font-weight: 500;
	}

	.emotions-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
		gap: 1.5rem;
		margin-bottom: 2rem;
	}

	.emotion-card {
		background: white;
		border: none;
		border-radius: 16px;
		padding: 2rem;
		cursor: pointer;
		transition: all 0.3s;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
		color: white;
		font-weight: 600;
	}

	.emotion-card:hover {
		transform: translateY(-8px) scale(1.05);
		box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
	}

	.emotion-emoji {
		font-size: 4rem;
		margin-bottom: 1rem;
	}

	.emotion-name {
		font-size: 1.5rem;
	}

	.tip-box {
		background: #e8f4f8;
		padding: 1.5rem;
		border-radius: 12px;
		border-left: 4px solid #3498db;
	}

	.tip-box h3 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 0.5rem;
	}

	.tip-box p {
		color: #2c3e50;
		line-height: 1.6;
		margin: 0;
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

	.selected-emotion-header {
		text-align: center;
		padding: 2rem;
		border-radius: 12px;
		color: white;
		margin-bottom: 2rem;
	}

	.big-emoji {
		font-size: 5rem;
		margin-bottom: 1rem;
	}

	.selected-emotion-header h2 {
		font-size: 2.5rem;
		margin: 0;
	}

	.intensity-prompt {
		text-align: center;
		font-size: 1.5rem;
		color: #2c3e50;
		margin-bottom: 2rem;
		font-weight: 500;
	}

	.intensity-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 1.5rem;
	}

	.intensity-card {
		background: white;
		border: 2px solid #e0e0e0;
		border-radius: 12px;
		padding: 2rem;
		cursor: pointer;
		transition: all 0.3s;
		text-align: center;
	}

	.intensity-card:hover {
		border-color: #3498db;
		transform: translateY(-4px);
		box-shadow: 0 6px 16px rgba(0, 0, 0, 0.1);
	}

	.intensity-card h3 {
		color: #2c3e50;
		margin-top: 0;
		margin-bottom: 1rem;
		font-size: 1.5rem;
	}

	.intensity-words {
		display: flex;
		flex-wrap: wrap;
		gap: 0.5rem;
		justify-content: center;
	}

	.word-badge {
		background: #ecf0f1;
		color: #2c3e50;
		padding: 0.5rem 1rem;
		border-radius: 20px;
		font-size: 0.9rem;
	}

	.feeling-summary {
		background: white;
		border: 3px solid;
		border-radius: 12px;
		padding: 2rem;
		display: flex;
		gap: 2rem;
		align-items: center;
		margin-bottom: 2rem;
	}

	.summary-emoji {
		font-size: 5rem;
	}

	.summary-text h2 {
		margin: 0 0 0.5rem 0;
		color: #2c3e50;
	}

	.intensity-level {
		color: #7f8c8d;
		font-size: 1.1rem;
		margin-bottom: 1rem;
		font-weight: 600;
	}

	.chosen-words {
		display: flex;
		flex-wrap: wrap;
		gap: 0.5rem;
	}

	.word {
		color: white;
		padding: 0.5rem 1rem;
		border-radius: 20px;
		font-size: 0.95rem;
		font-weight: 600;
	}

	.activities-box {
		background: #fff9e6;
		padding: 1.5rem;
		border-radius: 12px;
		margin-bottom: 1.5rem;
		border-left: 4px solid #f39c12;
	}

	.activities-box h3 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 1rem;
	}

	.activities-box ul {
		margin: 0;
		padding-left: 1.5rem;
	}

	.activities-box li {
		color: #2c3e50;
		line-height: 1.8;
		margin-bottom: 0.5rem;
		font-size: 1.05rem;
	}

	.parent-tip-box {
		background: #e8f4f8;
		padding: 1.5rem;
		border-radius: 12px;
		border-left: 4px solid;
		margin-bottom: 1.5rem;
	}

	.parent-tip-box h3 {
		margin-top: 0;
		color: #2c3e50;
		margin-bottom: 0.5rem;
	}

	.parent-tip-box p {
		color: #2c3e50;
		line-height: 1.6;
		margin: 0;
	}

	.action-buttons {
		text-align: center;
	}

	.reset-button {
		background: #95a5a6;
		color: white;
		border: none;
		padding: 1rem 2rem;
		border-radius: 8px;
		cursor: pointer;
		font-size: 1.1rem;
		transition: all 0.3s;
		font-weight: 600;
	}

	.reset-button:hover {
		background: #7f8c8d;
		transform: translateY(-2px);
	}

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}

		.feeling-summary {
			flex-direction: column;
			text-align: center;
		}

		.intensity-grid {
			grid-template-columns: 1fr;
		}

		.emotions-grid {
			grid-template-columns: repeat(2, 1fr);
		}
	}
</style>
