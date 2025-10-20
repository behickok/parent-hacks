<script>
	import { goto } from '$app/navigation';

	let dots = $state([]);
	let targetColor = $state('');
	let userAnswer = $state('');
	let message = $state('');
	let correctCount = $state(0);

	const colors = [
		{ name: 'red', hex: '#e74c3c', emoji: '🔴' },
		{ name: 'blue', hex: '#3498db', emoji: '🔵' },
		{ name: 'green', hex: '#27ae60', emoji: '🟢' },
		{ name: 'yellow', hex: '#f1c40f', emoji: '🟡' },
		{ name: 'purple', hex: '#9b59b6', emoji: '🟣' },
		{ name: 'orange', hex: '#e67e22', emoji: '🟠' }
	];

	function generateDots() {
		const totalDots = Math.floor(Math.random() * 25) + 15; // 15-40 dots
		const newDots = [];

		for (let i = 0; i < totalDots; i++) {
			const randomColor = colors[Math.floor(Math.random() * colors.length)];
			newDots.push({
				id: i,
				color: randomColor,
				x: Math.random() * 90 + 5, // 5-95%
				y: Math.random() * 90 + 5 // 5-95%
			});
		}

		dots = newDots;
		targetColor = colors[Math.floor(Math.random() * colors.length)];
		userAnswer = '';
		message = '';
	}

	function checkAnswer() {
		const actualCount = dots.filter((dot) => dot.color.name === targetColor.name).length;

		if (parseInt(userAnswer) === actualCount) {
			message = 'Correct! Take a deep breath...';
			correctCount++;
			setTimeout(() => {
				generateDots();
			}, 1500);
		} else {
			message = `Not quite. There are ${actualCount} ${targetColor.name} dots. Try again!`;
			setTimeout(() => {
				message = '';
			}, 2000);
		}
	}

	function handleKeyPress(event) {
		if (event.key === 'Enter') {
			checkAnswer();
		}
	}

	// Generate initial dots
	generateDots();
</script>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>Calming Activity</h1>
		<div class="score">Streak: {correctCount}</div>
	</header>

	<div class="instructions">
		<p>Take a moment to breathe and count the dots.</p>
		<p>Focus on the present moment.</p>
	</div>

	<div class="activity-container">
		<div class="dots-area">
			{#each dots as dot (dot.id)}
				<div
					class="dot"
					style="
						background: {dot.color.hex};
						left: {dot.x}%;
						top: {dot.y}%;
					"
				></div>
			{/each}
		</div>

		<div class="question-area">
			<div class="question">
				<span class="color-indicator" style="color: {targetColor.hex}">
					{targetColor.emoji}
				</span>
				<span>How many {targetColor.name} dots?</span>
			</div>

			<div class="answer-section">
				<input
					type="number"
					bind:value={userAnswer}
					onkeypress={handleKeyPress}
					placeholder="Enter number"
					min="0"
					autofocus
				/>
				<button onclick={checkAnswer} disabled={!userAnswer}>Check</button>
			</div>

			{#if message}
				<div class="message" class:correct={message.includes('Correct')}>
					{message}
				</div>
			{/if}
		</div>

		<button class="new-button" onclick={generateDots}>New Pattern</button>
	</div>
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

	.score {
		background: #27ae60;
		color: white;
		padding: 0.75rem 1.5rem;
		border-radius: 8px;
		font-weight: 600;
		font-size: 1rem;
	}

	.instructions {
		text-align: center;
		color: #7f8c8d;
		margin-bottom: 2rem;
		font-size: 1.1rem;
	}

	.instructions p {
		margin: 0.5rem 0;
	}

	.activity-container {
		background: white;
		border-radius: 12px;
		padding: 2rem;
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
	}

	.dots-area {
		position: relative;
		width: 100%;
		height: 400px;
		background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
		border-radius: 12px;
		margin-bottom: 2rem;
		border: 2px solid #e0e0e0;
	}

	.dot {
		position: absolute;
		width: 24px;
		height: 24px;
		border-radius: 50%;
		transform: translate(-50%, -50%);
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
		animation: appear 0.5s ease;
	}

	@keyframes appear {
		from {
			transform: translate(-50%, -50%) scale(0);
			opacity: 0;
		}
		to {
			transform: translate(-50%, -50%) scale(1);
			opacity: 1;
		}
	}

	.question-area {
		text-align: center;
	}

	.question {
		font-size: 1.8rem;
		color: #2c3e50;
		margin-bottom: 1.5rem;
		font-weight: 600;
		display: flex;
		align-items: center;
		justify-content: center;
		gap: 0.5rem;
	}

	.color-indicator {
		font-size: 2.5rem;
	}

	.answer-section {
		display: flex;
		gap: 1rem;
		justify-content: center;
		align-items: center;
		margin-bottom: 1rem;
	}

	.answer-section input {
		padding: 1rem;
		font-size: 1.5rem;
		border: 2px solid #e0e0e0;
		border-radius: 8px;
		width: 150px;
		text-align: center;
	}

	.answer-section input:focus {
		outline: none;
		border-color: #3498db;
	}

	.answer-section button {
		padding: 1rem 2rem;
		font-size: 1.2rem;
		background: #3498db;
		color: white;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		transition: all 0.3s;
		font-weight: 600;
	}

	.answer-section button:hover:not(:disabled) {
		background: #2980b9;
		transform: translateY(-2px);
	}

	.answer-section button:disabled {
		background: #95a5a6;
		cursor: not-allowed;
	}

	.message {
		margin-top: 1rem;
		padding: 1rem;
		border-radius: 8px;
		font-size: 1.2rem;
		font-weight: 600;
		background: #ffe6e6;
		color: #c0392b;
		animation: fade-in 0.3s ease;
	}

	.message.correct {
		background: #d4edda;
		color: #27ae60;
	}

	@keyframes fade-in {
		from {
			opacity: 0;
			transform: translateY(-10px);
		}
		to {
			opacity: 1;
			transform: translateY(0);
		}
	}

	.new-button {
		margin-top: 1.5rem;
		padding: 0.75rem 1.5rem;
		font-size: 1rem;
		background: #95a5a6;
		color: white;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		transition: all 0.3s;
		display: block;
		margin-left: auto;
		margin-right: auto;
	}

	.new-button:hover {
		background: #7f8c8d;
		transform: translateY(-2px);
	}

	@media (max-width: 600px) {
		.dots-area {
			height: 300px;
		}

		.answer-section {
			flex-direction: column;
		}

		.answer-section input,
		.answer-section button {
			width: 100%;
		}

		.question {
			font-size: 1.4rem;
		}

		.dot {
			width: 20px;
			height: 20px;
		}
	}
</style>
