<script>
	import { goto } from '$app/navigation';

	let dots = $state([]);
	let targetColor = $state('');
	let answerOptions = $state([]);
	let message = $state('');
	let correctCount = $state(0);
	let actualCount = $state(0);

	const colors = [
		{ name: 'red', hex: '#e74c3c', emoji: '🔴' },
		{ name: 'blue', hex: '#3b82f6', emoji: '🔵' },
		{ name: 'green', hex: '#27ae60', emoji: '🟢' },
		{ name: 'yellow', hex: '#f1c40f', emoji: '🟡' },
		{ name: 'purple', hex: '#9b59b6', emoji: '🟣' },
		{ name: 'orange', hex: '#e67e22', emoji: '🟠' }
	];

	function generateAnswerOptions(correctAnswer) {
		const options = new Set([correctAnswer]);

		// Add options around the correct answer
		const offsets = [-2, -1, 1, 2];
		for (const offset of offsets) {
			const option = correctAnswer + offset;
			if (option >= 0 && options.size < 5) {
				options.add(option);
			}
		}

		// If we still need more options (e.g., correct answer is very low)
		while (options.size < 5) {
			const random = Math.max(0, correctAnswer + Math.floor(Math.random() * 5) - 2);
			options.add(random);
		}

		// Convert to array and shuffle
		const optionsArray = Array.from(options);
		for (let i = optionsArray.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[optionsArray[i], optionsArray[j]] = [optionsArray[j], optionsArray[i]];
		}

		return optionsArray;
	}

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
		actualCount = newDots.filter((dot) => dot.color.name === targetColor.name).length;
		answerOptions = generateAnswerOptions(actualCount);
		message = '';
	}

	function checkAnswer(selectedAnswer) {
		if (selectedAnswer === actualCount) {
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

	// Generate initial dots
	generateDots();
</script>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>↩︎ Back</button>
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

			<div class="answer-buttons">
				{#each answerOptions as option}
					<button class="answer-button" onclick={() => checkAnswer(option)}>
						{option}
					</button>
				{/each}
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
	:global(body) {
		background: linear-gradient(135deg, #f0f9ff via #fdf2f8 to #fef3c7);
	}

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
		color: #1f2937;
		flex: 1;
		text-align: center;
	}

	.back-button {
		background: transparent;
		color: #374151;
		border: 1px solid #d1d5db;
		padding: 0.75rem 1.5rem;
		border-radius: 9999px;
		cursor: pointer;
		font-size: 1rem;
		transition: all 0.3s;
	}

	.back-button:hover {
		background: rgba(255, 255, 255, 0.8);
		border-color: #9ca3af;
	}

	.score {
		background: #27ae60;
		color: white;
		padding: 0.75rem 1.5rem;
		border-radius: 1rem;
		font-weight: 600;
		font-size: 1rem;
	}

	.instructions {
		text-align: center;
		color: #6b7280;
		margin-bottom: 2rem;
		font-size: 1.1rem;
	}

	.instructions p {
		margin: 0.5rem 0;
	}

	.activity-container {
		background: rgba(255, 255, 255, 0.8);
		border-radius: 1rem;
		padding: 2rem;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
	}

	.dots-area {
		position: relative;
		width: 100%;
		height: 400px;
		background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
		border-radius: 1rem;
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
		color: #1f2937;
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

	.answer-buttons {
		display: flex;
		gap: 1rem;
		justify-content: center;
		align-items: center;
		margin-bottom: 1rem;
		flex-wrap: wrap;
	}

	.answer-button {
		padding: 1.25rem 2rem;
		font-size: 1.5rem;
		background: #3b82f6;
		color: white;
		border: none;
		border-radius: 1rem;
		cursor: pointer;
		transition: all 0.3s;
		font-weight: 600;
		min-width: 80px;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
	}

	.answer-button:hover {
		background: #2563eb;
		transform: translateY(-2px);
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
	}

	.answer-button:active {
		transform: translateY(0);
		box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
	}

	.message {
		margin-top: 1rem;
		padding: 1rem;
		border-radius: 1rem;
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
		border-radius: 1rem;
		cursor: pointer;
		transition: all 0.3s;
		display: block;
		margin-left: auto;
		margin-right: auto;
	}

	.new-button:hover {
		background: #6b7280;
		transform: translateY(-2px);
	}

	@media (max-width: 600px) {
		.dots-area {
			height: 300px;
		}

		.answer-buttons {
			gap: 0.75rem;
		}

		.answer-button {
			padding: 1rem 1.5rem;
			font-size: 1.3rem;
			min-width: 70px;
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
