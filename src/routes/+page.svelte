<script>
	import { goto } from '$app/navigation';
</script>

<div class="container">
	<header>
		<img src="/logo.png" alt="Parent Hacks Logo" class="logo" />
		<h1>Parent Hacks</h1>
		<p>Helpful tools for parents of toddlers and young kids</p>
	</header>

	<section class="category-section">
		<h2 class="category-title">⏰ Timers & Trackers</h2>
		<div class="tools-grid">
			<button class="tool-card" onclick={() => goto('/visual-timer')}>
				<div class="tool-icon">⏰</div>
				<h3>Visual Timer</h3>
				<p>Color-coded countdown timer</p>
			</button>

			<button class="tool-card" onclick={() => goto('/transition-timer')}>
				<div class="tool-icon">🎵</div>
				<h3>Transition Timer</h3>
				<p>Musical timers for cleanup</p>
			</button>

			<button class="tool-card" onclick={() => goto('/pompom-tracker')}>
				<div class="tool-icon">🎨</div>
				<h3>Pom Pom Tracker</h3>
				<p>Visual reward system</p>
			</button>
		</div>
	</section>

	<section class="category-section">
		<h2 class="category-title">📋 Routines & Organization</h2>
		<div class="tools-grid">
			<button class="tool-card" onclick={() => goto('/routine-builder')}>
				<div class="tool-icon">📋</div>
				<h3>Routine Builder</h3>
				<p>Custom visual schedules</p>
			</button>

			<button class="tool-card" onclick={() => goto('/chore-chart')}>
				<div class="tool-icon">✅</div>
				<h3>Chore Chart</h3>
				<p>Age-appropriate chores</p>
			</button>

			<button class="tool-card" onclick={() => goto('/activity-generator')}>
				<div class="tool-icon">🎲</div>
				<h3>Activity Generator</h3>
				<p>Quiet time & sensory ideas</p>
			</button>
		</div>
	</section>

	<section class="category-section">
		<h2 class="category-title">💭 Emotions & Behavior</h2>
		<div class="tools-grid">
			<button class="tool-card" onclick={() => goto('/feelings-wheel')}>
				<div class="tool-icon">💭</div>
				<h3>Feelings Wheel</h3>
				<p>Identify and express emotions</p>
			</button>

			<button class="tool-card" onclick={() => goto('/calming-activity')}>
				<div class="tool-icon">🧘</div>
				<h3>Calming Activity</h3>
				<p>Count dots to calm down</p>
			</button>

			<button class="tool-card" onclick={() => goto('/conflict-mediator')}>
				<div class="tool-icon">🤝</div>
				<h3>Conflict Mediator</h3>
				<p>Resolve sibling conflicts</p>
			</button>
		</div>
	</section>

	<section class="category-section">
		<h2 class="category-title">📚 Parent Resources</h2>
		<div class="tools-grid">
			<button class="tool-card" onclick={() => goto('/script-library')}>
				<div class="tool-icon">📚</div>
				<h3>Script Library</h3>
				<p>Scripts for tough situations</p>
			</button>

			<button class="tool-card" onclick={() => goto('/consequences-guide')}>
				<div class="tool-icon">🎯</div>
				<h3>Consequences Guide</h3>
				<p>Natural & logical consequences</p>
			</button>

			<button class="tool-card" onclick={() => goto('/resources')}>
				<div class="tool-icon">📖</div>
				<h3>Resources</h3>
				<p>Articles from experts</p>
			</button>

			<button class="tool-card" onclick={() => goto('/product-recommendations')}>
				<div class="tool-icon">🛍️</div>
				<h3>Product Recommendations</h3>
				<p>Helpful products for parents</p>
			</button>
		</div>
	</section>
</div>

<style>
	.container {
		max-width: 1200px;
		margin: 0 auto;
		padding: 2rem;
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
		font-size: 3rem;
		color: #2c3e50;
		margin-bottom: 0.5rem;
	}

	header p {
		font-size: 1.2rem;
		color: #7f8c8d;
	}

	.category-section {
		margin-bottom: 3rem;
	}

	.category-title {
		font-size: 1.5rem;
		color: #2c3e50;
		margin-bottom: 1rem;
		padding-bottom: 0.5rem;
		border-bottom: 3px solid #3498db;
	}

	.tools-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
		gap: 1.5rem;
	}

	.tool-card {
		background: white;
		border: 2px solid #e0e0e0;
		border-radius: 12px;
		padding: 1.5rem;
		text-align: center;
		cursor: pointer;
		transition: all 0.3s ease;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
		min-height: 180px;
		display: flex;
		flex-direction: column;
		justify-content: center;
	}

	.tool-card:hover {
		transform: translateY(-4px);
		box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
		border-color: #3498db;
	}

	.tool-icon {
		font-size: 3.5rem;
		margin-bottom: 0.75rem;
	}

	.tool-card h3 {
		font-size: 1.2rem;
		color: #2c3e50;
		margin-bottom: 0.5rem;
		margin-top: 0;
		word-wrap: break-word;
		overflow-wrap: break-word;
	}

	.tool-card p {
		color: #7f8c8d;
		font-size: 0.9rem;
		margin: 0;
		line-height: 1.4;
		word-wrap: break-word;
		overflow-wrap: break-word;
	}

	@media (max-width: 768px) {
		.tools-grid {
			grid-template-columns: 1fr;
		}

		header h1 {
			font-size: 2rem;
		}

		.category-title {
			font-size: 1.3rem;
		}
	}
</style>
