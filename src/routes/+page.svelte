<script>
	import { goto } from '$app/navigation';
</script>

<div class="container">
	<header>
		<img src="/logo.png" alt="Parent Hacks Logo" class="logo" />
		<h1>Parent Hacks</h1>
		<p>Helpful tools for parents of toddlers and young kids</p>
	</header>

	<div class="tools-grid">
		<button class="tool-card" onclick={() => goto('/pompom-tracker')}>
			<div class="tool-icon">🎨</div>
			<h2>Pom Pom Tracker</h2>
			<p>Visual reward system with customizable themes</p>
		</button>

		<button class="tool-card" onclick={() => goto('/calming-activity')}>
			<div class="tool-icon">🧘</div>
			<h2>Calming Activity</h2>
			<p>Count colored dots to calm your mind</p>
		</button>

		<button class="tool-card" onclick={() => goto('/visual-timer')}>
			<div class="tool-icon">⏰</div>
			<h2>Visual Timer</h2>
			<p>Color-coded countdown timer for kids</p>
		</button>

		<button class="tool-card" onclick={() => goto('/feelings-wheel')}>
			<div class="tool-icon">💭</div>
			<h2>Feelings Wheel</h2>
			<p>Help kids identify and express their emotions</p>
		</button>

		<button class="tool-card" onclick={() => goto('/routine-builder')}>
			<div class="tool-icon">📋</div>
			<h2>Routine Builder</h2>
			<p>Create custom visual schedules and routines</p>
		</button>

		<button class="tool-card" onclick={() => goto('/script-library')}>
			<div class="tool-icon">📚</div>
			<h2>Script Library</h2>
			<p>Helpful scripts for common parenting situations</p>
		</button>

		<button class="tool-card" onclick={() => goto('/resources')}>
			<div class="tool-icon">📖</div>
			<h2>Resources</h2>
			<p>Curated articles from parenting experts</p>
		</button>
	</div>
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

	.tools-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 2rem;
		margin-top: 2rem;
	}

	.tool-card {
		background: white;
		border: 2px solid #e0e0e0;
		border-radius: 12px;
		padding: 2rem;
		text-align: center;
		cursor: pointer;
		transition: all 0.3s ease;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.tool-card:hover {
		transform: translateY(-4px);
		box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
		border-color: #3498db;
	}

	.tool-icon {
		font-size: 4rem;
		margin-bottom: 1rem;
	}

	.tool-card h2 {
		font-size: 1.5rem;
		color: #2c3e50;
		margin-bottom: 0.5rem;
	}

	.tool-card p {
		color: #7f8c8d;
		font-size: 0.95rem;
	}
</style>
