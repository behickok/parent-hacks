<script>
	import { goto } from '$app/navigation';
</script>

<div class="page-wrapper">
	<div class="container">
		<header>
			<img src="/logo.png" alt="Parent Hacks Logo" class="logo" />
			<h1>Parent Hacks</h1>
			<p>Helpful tools for parents of toddlers and young kids</p>
		</header>

		<section class="category-section">
			<h2 class="category-title">⏰ Timers & Trackers</h2>
			<div class="tools-grid">
				<div
					class="tool-card"
					onclick={() => goto('/visual-timer')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/visual-timer');
					}}
				>
					<div class="tool-icon">⏰</div>
					<h3>Visual Timer</h3>
					<p>Color-coded countdown timer</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/visual-timer');
						}}>Open</button
					>
				</div>

				<div
					class="tool-card"
					onclick={() => goto('/transition-timer')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/transition-timer');
					}}
				>
					<div class="tool-icon">🎵</div>
					<h3>Transition Timer</h3>
					<p>Musical timers for cleanup</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/transition-timer');
						}}>Open</button
					>
				</div>

				<div
					class="tool-card"
					onclick={() => goto('/pompom-tracker')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/pompom-tracker');
					}}
				>
					<div class="tool-icon">🎨</div>
					<h3>Pom Pom Tracker</h3>
					<p>Visual reward system</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/pompom-tracker');
						}}>Open</button
					>
				</div>
			</div>
		</section>

		<section class="category-section">
			<h2 class="category-title">📋 Routines & Organization</h2>
			<div class="tools-grid">
				<div
					class="tool-card"
					onclick={() => goto('/routine-builder')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/routine-builder');
					}}
				>
					<div class="tool-icon">📋</div>
					<h3>Routine Builder</h3>
					<p>Custom visual schedules</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/routine-builder');
						}}>Open</button
					>
				</div>

				<div
					class="tool-card"
					onclick={() => goto('/chore-chart')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/chore-chart');
					}}
				>
					<div class="tool-icon">✅</div>
					<h3>Chore Chart</h3>
					<p>Age-appropriate chores</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/chore-chart');
						}}>Open</button
					>
				</div>

				<div
					class="tool-card"
					onclick={() => goto('/activity-generator')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/activity-generator');
					}}
				>
					<div class="tool-icon">🎲</div>
					<h3>Activity Generator</h3>
					<p>Quiet time & sensory ideas</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/activity-generator');
						}}>Open</button
					>
				</div>
			</div>
		</section>

		<section class="category-section">
			<h2 class="category-title">💭 Emotions & Behavior</h2>
			<div class="tools-grid">
				<div
					class="tool-card"
					onclick={() => goto('/feelings-wheel')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/feelings-wheel');
					}}
				>
					<div class="tool-icon">💭</div>
					<h3>Feelings Wheel</h3>
					<p>Identify and express emotions</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/feelings-wheel');
						}}>Open</button
					>
				</div>

				<div
					class="tool-card"
					onclick={() => goto('/calming-activity')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/calming-activity');
					}}
				>
					<div class="tool-icon">🧘</div>
					<h3>Calming Activity</h3>
					<p>Count dots to calm down</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/calming-activity');
						}}>Open</button
					>
				</div>

				<div
					class="tool-card"
					onclick={() => goto('/conflict-mediator')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/conflict-mediator');
					}}
				>
					<div class="tool-icon">🤝</div>
					<h3>Conflict Mediator</h3>
					<p>Resolve sibling conflicts</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/conflict-mediator');
						}}>Open</button
					>
				</div>
			</div>
		</section>

		<section class="category-section">
			<h2 class="category-title">📚 Parent Resources</h2>
			<div class="tools-grid">
				<div
					class="tool-card"
					onclick={() => goto('/script-library')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/script-library');
					}}
				>
					<div class="tool-icon">📚</div>
					<h3>Script Library</h3>
					<p>Scripts for tough situations</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/script-library');
						}}>Open</button
					>
				</div>

				<div
					class="tool-card"
					onclick={() => goto('/consequences-guide')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/consequences-guide');
					}}
				>
					<div class="tool-icon">🎯</div>
					<h3>Consequences Guide</h3>
					<p>Natural & logical consequences</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/consequences-guide');
						}}>Open</button
					>
				</div>

				<div
					class="tool-card"
					onclick={() => goto('/resources')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/resources');
					}}
				>
					<div class="tool-icon">📖</div>
					<h3>Resources</h3>
					<p>Articles from experts</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/resources');
						}}>Open</button
					>
				</div>

				<div
					class="tool-card"
					onclick={() => goto('/product-recommendations')}
					role="button"
					tabindex="0"
					onkeydown={(e) => {
						if (e.key === 'Enter' || e.key === ' ') goto('/product-recommendations');
					}}
				>
					<div class="tool-icon">🛍️</div>
					<h3>Product Recommendations</h3>
					<p>Helpful products for parents</p>
					<button
						class="open-button"
						onclick={(e) => {
							e.stopPropagation();
							goto('/product-recommendations');
						}}>Open</button
					>
				</div>
			</div>
		</section>
	</div>
</div>

<style>
	:global(body) {
		background: linear-gradient(135deg, #f0f9ff via #fdf2f8 to #fef3c7);
		min-height: 100vh;
	}

	.page-wrapper {
		background: linear-gradient(135deg, #f0f9ff via #fdf2f8 to #fef3c7);
		min-height: 100vh;
		padding: 2rem;
	}

	.container {
		max-width: 1200px;
		margin: 0 auto;
	}

	header {
		text-align: center;
		margin-bottom: 3rem;
	}

	.logo {
		width: 120px;
		height: auto;
		margin-bottom: 1rem;
		display: block;
		margin-left: auto;
		margin-right: auto;
	}

	header h1 {
		font-size: 2.5rem;
		font-weight: 700;
		color: #1f2937;
		margin-bottom: 0.75rem;
		margin-top: 0;
	}

	header p {
		font-size: 1.1rem;
		color: #6b7280;
		max-width: 500px;
		margin: 0 auto;
	}

	.category-section {
		margin-bottom: 3.5rem;
	}

	.category-title {
		font-size: 1.4rem;
		font-weight: 600;
		color: #1f2937;
		margin-bottom: 1.5rem;
		margin-top: 0;
		padding-bottom: 0.75rem;
		border-bottom: 1px solid #e5e7eb;
	}

	.tools-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
		gap: 1.5rem;
	}

	.tool-card {
		background: rgba(255, 255, 255, 0.8);
		border: none;
		border-radius: 1rem;
		padding: 1.75rem 1.5rem;
		text-align: center;
		cursor: pointer;
		transition: all 0.3s ease;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
		min-height: 200px;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		outline: none;
	}

	.tool-card:hover {
		background: rgba(255, 255, 255, 0.95);
		box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
		transform: translateY(-2px);
	}

	.tool-card:focus {
		outline: 2px solid #3b82f6;
		outline-offset: 2px;
	}

	.tool-card:focus-visible {
		outline: 2px solid #3b82f6;
		outline-offset: 2px;
	}

	.tool-icon {
		font-size: 3rem;
		margin-bottom: 1rem;
		line-height: 1;
	}

	.tool-card h3 {
		font-size: 1.1rem;
		font-weight: 600;
		color: #1f2937;
		margin-bottom: 0.5rem;
		margin-top: 0;
		word-wrap: break-word;
		overflow-wrap: break-word;
	}

	.tool-card p {
		color: #6b7280;
		font-size: 0.875rem;
		margin: 0 0 1rem 0;
		line-height: 1.5;
		word-wrap: break-word;
		overflow-wrap: break-word;
	}

	.open-button {
		background: transparent;
		border: 1px solid #d1d5db;
		border-radius: 9999px;
		color: #374151;
		font-size: 0.875rem;
		padding: 0.375rem 1rem;
		cursor: pointer;
		transition: all 0.2s ease;
		font-weight: 500;
	}

	.open-button:hover {
		background: #f3f4f6;
		border-color: #9ca3af;
		color: #1f2937;
	}

	@media (max-width: 768px) {
		.page-wrapper {
			padding: 1rem;
		}

		.tools-grid {
			grid-template-columns: 1fr;
		}

		header h1 {
			font-size: 2rem;
		}

		header p {
			font-size: 1rem;
		}

		.category-title {
			font-size: 1.2rem;
		}
	}
</style>
