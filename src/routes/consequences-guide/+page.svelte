<script>
	import { goto } from '$app/navigation';

	let selectedCategory = $state(null);
	let selectedScenario = $state(null);

	const scenarios = {
		daily: {
			title: 'Daily Routines',
			icon: '🏠',
			color: '#3b82f6',
			situations: [
				{
					behavior: 'Refusing to get dressed in the morning',
					natural: {
						consequence: 'They go to school/daycare in pajamas (if safe and weather-appropriate)',
						explanation:
							'The child experiences the natural embarrassment or consequence of their choice.',
						when: 'Safe environment, not too cold/hot, not a special event day'
					},
					logical: {
						consequence:
							'Limited clothing choices tomorrow - you pick two options, they choose one',
						explanation:
							'Related to the problem but parent-imposed. Teaches decision-making within limits.',
						when: "Natural consequence isn't safe or appropriate"
					},
					avoid: [
						'Yelling or power struggles',
						'Letting them be late repeatedly',
						"Dressing them when they're capable",
						'Long lectures about responsibility'
					],
					script:
						"\"You're choosing not to get dressed. That means you'll wear pajamas today. Tomorrow, I'll pick out two outfits and you can choose one.\""
				},
				{
					behavior: "Won't eat dinner",
					natural: {
						consequence: 'They get hungry before next meal/snack time',
						explanation: 'Hunger is the natural consequence of not eating.',
						when: 'Child is healthy, no food insecurity issues, regular meal schedule'
					},
					logical: {
						consequence: 'No dessert or special snacks; kitchen closes after dinner time',
						explanation: "Logically related - if you don't eat meals, you don't get treats.",
						when: 'Always appropriate for healthy children'
					},
					avoid: [
						'Making separate meals',
						'Forcing them to eat',
						'Using food as punishment or reward',
						'Late-night snacks to compensate'
					],
					script:
						'"I can see you\'re not hungry for dinner. Kitchen closes in 10 minutes. Breakfast is at [time] tomorrow morning."'
				},
				{
					behavior: 'Dawdling and making everyone late',
					natural: {
						consequence: 'Miss the fun part of the activity; arrive after friends',
						explanation: 'They experience the disappointment of missing out.',
						when: "It won't cause serious problems (work, appointments)"
					},
					logical: {
						consequence:
							'Lose privilege of the activity next time; earlier bedtime to wake up on time',
						explanation: "Related to time management and respecting others' schedules.",
						when: 'Being late affects others significantly'
					},
					avoid: [
						'Doing everything for them to speed up',
						'Chronic lateness becoming the norm',
						'Blaming the child publicly',
						'Skipping consequences out of guilt'
					],
					script:
						"\"We needed to leave at 3:00 and it's 3:15. We'll go now, but you'll miss [first part]. Next time, you can be ready on time or we won't go.\""
				}
			]
		},
		toys: {
			title: 'Toys & Belongings',
			icon: '🧸',
			color: '#e74c3c',
			situations: [
				{
					behavior: 'Not cleaning up toys',
					natural: {
						consequence: "Can't find favorite toys; stepping on toys hurts; room is messy",
						explanation: 'They live with the mess they created.',
						when: 'Mess is contained to their space, not a safety hazard'
					},
					logical: {
						consequence: 'Toys left out get put in "toy jail" for 24 hours or until earned back',
						explanation: 'Related to caring for belongings - lose access if not respected.',
						when: 'Toys all over common areas; repeatedly ignoring cleanup'
					},
					avoid: [
						'Cleaning up for them',
						'Empty threats',
						'Throwing toys away in anger',
						'Expecting perfection'
					],
					script:
						'"Toys that aren\'t cleaned up by bedtime will go in the timeout bin for tomorrow. You can earn them back by cleaning up your other toys."'
				},
				{
					behavior: 'Breaking toys due to rough play',
					natural: {
						consequence: "Toy is broken and can't be played with anymore",
						explanation: "They can't use something they broke.",
						when: 'Always - this is a perfect natural consequence'
					},
					logical: {
						consequence: 'No replacement toy; may need to save allowance to replace it',
						explanation: 'Learn that belongings cost money and must be cared for.',
						when: 'When pattern of carelessness emerges'
					},
					avoid: [
						'Immediately buying a replacement',
						'Fixing everything for them',
						'Harsh punishment for accidents',
						'Saying "I told you so"'
					],
					script:
						"\"Oh no, your toy broke because you [specific action]. That's sad. Toys don't work when they're broken. We won't be replacing it right now.\""
				},
				{
					behavior: 'Fighting over toys with siblings',
					natural: {
						consequence: 'Neither child gets the toy; play becomes unpleasant',
						explanation: 'Fighting makes play time unhappy for everyone.',
						when: 'Minor squabbles; no physical aggression'
					},
					logical: {
						consequence: 'Toy is removed for remainder of day; both children lose access',
						explanation:
							"Related to sharing and taking turns - can't have it if you fight over it.",
						when: 'Repeated fighting; safety concerns'
					},
					avoid: [
						'Always determining who\'s "right"',
						'Forcing sharing immediately',
						'Punishing only one child',
						'Allowing fighting to continue'
					],
					script:
						'"You\'re both fighting over this toy. That tells me you\'re not ready to share it. The toy is going away for today. You can try again tomorrow."'
				}
			]
		},
		behavior: {
			title: 'Misbehavior & Choices',
			icon: '⚠️',
			color: '#f39c12',
			situations: [
				{
					behavior: 'Throwing tantrum in public',
					natural: {
						consequence: 'Activity ends; leave the store/park immediately',
						explanation: 'Behavior that disrupts others ends the fun.',
						when: 'Behavior is disruptive but not dangerous'
					},
					logical: {
						consequence:
							"Don't bring them to that type of place for a set period; practice behavior at home first",
						explanation: 'Related to public behavior - must demonstrate readiness.',
						when: 'Pattern of public meltdowns; need to rebuild skills'
					},
					avoid: [
						'Giving in to demands to stop tantrum',
						'Screaming back at them',
						'Spanking or physical punishment',
						'Extended public lectures'
					],
					script:
						"\"I can see you're having trouble controlling your body/voice. We're leaving now. You can try again when you're ready to [expected behavior].\""
				},
				{
					behavior: 'Deliberately hurting siblings or pets',
					natural: {
						consequence: 'Sibling/pet avoids them; relationship is damaged',
						explanation: 'Hurting others makes them not want to be near you.',
						when: "Supplement with logical consequences; natural isn't enough"
					},
					logical: {
						consequence:
							'Immediate separation; lose privilege of playing together; must make amends',
						explanation: "Related to safety - can't be together if someone gets hurt.",
						when: 'Always - safety is non-negotiable'
					},
					avoid: [
						'Only punishing without teaching',
						'Allowing "accidents" repeatedly',
						'Forcing apologies without understanding',
						'Hitting them to show hitting is wrong'
					],
					script:
						"\"I won't let you hurt [sibling/pet]. Hurting is not okay. You need to sit here until you're calm. Then we'll talk about making it right.\""
				},
				{
					behavior: 'Lying about something they did',
					natural: {
						consequence: "Trust is damaged; people don't believe them",
						explanation: 'Lying breaks trust in relationships.',
						when: 'Age-appropriate to understand trust; not too young'
					},
					logical: {
						consequence: 'Loss of privilege related to what they lied about; extra check-ins',
						explanation: 'Related to honesty - need to rebuild trust.',
						when: 'Pattern of lying; serious lies'
					},
					avoid: [
						'Harsh punishment that encourages more lying',
						'Calling them a "liar"',
						'Not addressing it at all',
						'Trapping them in lies'
					],
					script:
						'"That doesn\'t match what I know happened. When you tell me something that\'s not true, it makes it hard for me to trust you. I need you to tell me the truth now."'
				}
			]
		},
		responsibility: {
			title: 'Responsibility & Care',
			icon: '📋',
			color: '#27ae60',
			situations: [
				{
					behavior: 'Forgetting homework/lunch/sports gear',
					natural: {
						consequence: "Face consequences at school (missed recess, can't participate, etc.)",
						explanation: "They experience the school's response to being unprepared.",
						when: 'Safe to do so; school has reasonable consequences; child is old enough (typically 7+)'
					},
					logical: {
						consequence:
							'Create checklist; lose screen time to practice packing; pack together until mastered',
						explanation: 'Related to organization - need to build systems.',
						when: 'Too young for full natural consequence; pattern of forgetting'
					},
					avoid: [
						'Constantly rescuing them',
						'Bringing forgotten items to school',
						'Making excuses for them',
						'Harsh punishment at home'
					],
					script:
						"\"I see you forgot [item]. That's disappointing. You'll need to face the consequence at school. Let's make a checklist together so this doesn't happen again.\""
				},
				{
					behavior: 'Not taking care of pet',
					natural: {
						consequence: 'Pet is unhappy, smelly, or uncomfortable',
						explanation: "Neglect has visible impact on pet's wellbeing.",
						when: "NEVER for pet's basic needs - parent must step in"
					},
					logical: {
						consequence:
							'Lose pet privileges; parent takes over care; no new pets/responsibilities',
						explanation: 'Related to caring for living things - proves not ready.',
						when: 'Always - pet welfare comes first'
					},
					avoid: [
						'Letting pet suffer',
						'Getting rid of pet immediately',
						'Taking over without discussion',
						'Not giving chances to improve'
					],
					script:
						"\"[Pet] needs to be fed by 6pm every day. It's 7pm and they haven't eaten. I'll feed them tonight. If this happens again, I'll take over pet care and you won't be ready for a new pet.\""
				},
				{
					behavior: 'Wasting/destroying supplies (markers, paper, etc.)',
					natural: {
						consequence: 'No more supplies to use for projects/art',
						explanation: "Running out means can't do the activity.",
						when: "Non-essential items; won't impact school work"
					},
					logical: {
						consequence:
							'Must earn money to replace; limited supplies given at a time; supervised use',
						explanation: 'Related to respecting resources - must prove responsibility.',
						when: 'Expensive items; repeated wastefulness'
					},
					avoid: [
						'Immediately replacing everything',
						'Banning art/creativity entirely',
						'Not teaching proper use',
						'Disproportionate punishment'
					],
					script:
						'"You used up all the markers by [wasteful action]. Now there are no markers left. We\'ll get more when you can earn the money or in [timeframe]."'
				}
			]
		}
	};

	function selectCategory(category) {
		selectedCategory = category;
		selectedScenario = null;
	}

	function selectScenario(scenario) {
		selectedScenario = scenario;
	}

	function reset() {
		selectedCategory = null;
		selectedScenario = null;
	}

	function copyScript(text) {
		navigator.clipboard.writeText(text);
		alert('Script copied to clipboard!');
	}
</script>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>↩︎ Back</button>
		<h1>Consequences Guide</h1>
		<div style="width: 100px;"></div>
	</header>

	<div class="intro">
		<p>
			Learn the difference between natural consequences, logical consequences, and punishment. Find
			age-appropriate, effective responses to common behaviors.
		</p>
	</div>

	{#if !selectedCategory}
		<div class="explainer">
			<h2>Understanding Consequences</h2>
			<div class="consequence-types">
				<div class="type-card natural">
					<h3>🌱 Natural Consequences</h3>
					<p>
						What happens naturally as a result of the child's action, without parent intervention.
					</p>
					<div class="example">
						<strong>Example:</strong> Child refuses coat → Gets cold outside
					</div>
				</div>

				<div class="type-card logical">
					<h3>🔗 Logical Consequences</h3>
					<p>
						Parent-imposed consequences that are directly related to the misbehavior and teach
						responsibility.
					</p>
					<div class="example">
						<strong>Example:</strong> Child throws food → Loses rest of meal
					</div>
				</div>

				<div class="type-card punishment">
					<h3>❌ Punishment (Avoid)</h3>
					<p>
						Consequences unrelated to behavior, often shame-based and punitive rather than teaching.
					</p>
					<div class="example">
						<strong>Example:</strong> Child won't share toy → Sent to room for an hour
					</div>
				</div>
			</div>
		</div>

		<div class="categories">
			<h2>Choose a Category</h2>
			<div class="categories-grid">
				{#each Object.entries(scenarios) as [key, category]}
					<button
						class="category-card"
						style="border-color: {category.color}"
						onclick={() => selectCategory(key)}
					>
						<div class="category-icon">{category.icon}</div>
						<h3>{category.title}</h3>
						<p>{category.situations.length} scenarios</p>
					</button>
				{/each}
			</div>
		</div>
	{:else if !selectedScenario}
		<div class="scenarios-section">
			<button class="breadcrumb-button" onclick={reset}>↩︎ Back to Categories</button>

			<h2 style="color: {scenarios[selectedCategory].color}">
				{scenarios[selectedCategory].icon}
				{scenarios[selectedCategory].title}
			</h2>

			<div class="scenarios-list">
				{#each scenarios[selectedCategory].situations as situation}
					<button class="scenario-card" onclick={() => selectScenario(situation)}>
						<h3>{situation.behavior}</h3>
						<span class="arrow">→</span>
					</button>
				{/each}
			</div>
		</div>
	{:else}
		<div class="detail-section">
			<button class="breadcrumb-button" onclick={() => (selectedScenario = null)}>
				↩︎ Back to {scenarios[selectedCategory].title}
			</button>

			<div class="scenario-header">
				<h1>{selectedScenario.behavior}</h1>
			</div>

			<div class="consequence-option natural">
				<h3>🌱 Natural Consequence</h3>
				<div class="consequence-detail">
					<div class="what">
						<strong>What happens:</strong>
						<p>{selectedScenario.natural.consequence}</p>
					</div>
					<div class="why">
						<strong>Why it works:</strong>
						<p>{selectedScenario.natural.explanation}</p>
					</div>
					<div class="when">
						<strong>When to use:</strong>
						<p>{selectedScenario.natural.when}</p>
					</div>
				</div>
			</div>

			<div class="consequence-option logical">
				<h3>🔗 Logical Consequence</h3>
				<div class="consequence-detail">
					<div class="what">
						<strong>What to do:</strong>
						<p>{selectedScenario.logical.consequence}</p>
					</div>
					<div class="why">
						<strong>Why it works:</strong>
						<p>{selectedScenario.logical.explanation}</p>
					</div>
					<div class="when">
						<strong>When to use:</strong>
						<p>{selectedScenario.logical.when}</p>
					</div>
				</div>
			</div>

			{#if selectedScenario.script}
				<div class="script-box">
					<h3>What to Say:</h3>
					<p class="script-text">{selectedScenario.script}</p>
					<button class="copy-button" onclick={() => copyScript(selectedScenario.script)}>
						📋 Copy Script
					</button>
				</div>
			{/if}

			<div class="avoid-box">
				<h3>❌ What to Avoid:</h3>
				<ul>
					{#each selectedScenario.avoid as item}
						<li>{item}</li>
					{/each}
				</ul>
			</div>

			<div class="principles-box">
				<h3>💡 Key Principles:</h3>
				<ul>
					<li><strong>Related:</strong> Consequence should relate to the misbehavior</li>
					<li><strong>Respectful:</strong> Maintain child's dignity; no shaming</li>
					<li><strong>Reasonable:</strong> Fits the age and severity of behavior</li>
					<li><strong>Revealed in advance:</strong> Child knows what to expect</li>
					<li>
						<strong>Repeated:</strong> Consistency is key - follow through every time
					</li>
				</ul>
			</div>
		</div>
	{/if}
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

	.intro {
		text-align: center;
		font-size: 1.1rem;
		color: #1f2937;
		margin-bottom: 2rem;
		line-height: 1.6;
	}

	.explainer {
		background: rgba(255, 255, 255, 0.8);
		padding: 2rem;
		border-radius: 1rem;
		margin-bottom: 2rem;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
	}

	.explainer h2 {
		margin-top: 0;
		color: #1f2937;
		text-align: center;
		margin-bottom: 2rem;
	}

	.consequence-types {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 1.5rem;
	}

	.type-card {
		padding: 1.5rem;
		border-radius: 1rem;
		border: 2px solid;
	}

	.type-card.natural {
		border-color: #27ae60;
		background: #e8f8f5;
	}

	.type-card.logical {
		border-color: #3b82f6;
		background: #e3f2fd;
	}

	.type-card.punishment {
		border-color: #e74c3c;
		background: #ffebee;
	}

	.type-card h3 {
		margin-top: 0;
		color: #1f2937;
		margin-bottom: 0.5rem;
	}

	.type-card p {
		color: #1f2937;
		line-height: 1.6;
		margin-bottom: 1rem;
	}

	.example {
		background: rgba(255, 255, 255, 0.7);
		padding: 1rem;
		border-radius: 6px;
		font-size: 0.95rem;
		color: #1f2937;
	}

	.categories {
		background: rgba(255, 255, 255, 0.8);
		padding: 2rem;
		border-radius: 1rem;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
	}

	.categories h2 {
		margin-top: 0;
		color: #1f2937;
		margin-bottom: 1.5rem;
	}

	.categories-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
		gap: 1.5rem;
	}

	.category-card {
		background: rgba(255, 255, 255, 0.8);
		border: 3px solid;
		border-radius: 1rem;
		padding: 2rem;
		cursor: pointer;
		transition: all 0.3s;
		text-align: center;
	}

	.category-card:hover {
		transform: translateY(-4px);
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.15);
	}

	.category-icon {
		font-size: 4rem;
		margin-bottom: 1rem;
	}

	.category-card h3 {
		margin: 0 0 0.5rem 0;
		color: #1f2937;
		font-size: 1.2rem;
	}

	.category-card p {
		margin: 0;
		color: #6b7280;
		font-size: 0.9rem;
	}

	.breadcrumb-button {
		background: #ecf0f1;
		border: none;
		padding: 0.5rem 1rem;
		border-radius: 6px;
		cursor: pointer;
		color: #1f2937;
		font-size: 0.95rem;
		transition: background 0.3s;
		margin-bottom: 1.5rem;
	}

	.breadcrumb-button:hover {
		background: #bdc3c7;
	}

	.scenarios-list {
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}

	.scenario-card {
		background: rgba(255, 255, 255, 0.8);
		border: 2px solid #e0e0e0;
		border-radius: 1rem;
		padding: 1.5rem;
		cursor: pointer;
		transition: all 0.3s;
		display: flex;
		justify-content: space-between;
		align-items: center;
		text-align: left;
	}

	.scenario-card:hover {
		border-color: #3b82f6;
		transform: translateX(4px);
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
	}

	.scenario-card h3 {
		margin: 0;
		color: #1f2937;
		font-size: 1.1rem;
	}

	.arrow {
		font-size: 1.5rem;
		color: #3b82f6;
	}

	.scenario-header {
		background: rgba(255, 255, 255, 0.8);
		padding: 2rem;
		border-radius: 1rem;
		margin-bottom: 2rem;
		box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
	}

	.scenario-header h1 {
		margin: 0;
		color: #1f2937;
		font-size: 2rem;
		text-align: center;
	}

	.consequence-option {
		padding: 1.5rem;
		border-radius: 1rem;
		margin-bottom: 1.5rem;
		border-left: 4px solid;
	}

	.consequence-option.natural {
		background: #e8f8f5;
		border-color: #27ae60;
	}

	.consequence-option.logical {
		background: #e3f2fd;
		border-color: #3b82f6;
	}

	.consequence-option h3 {
		margin-top: 0;
		color: #1f2937;
		margin-bottom: 1rem;
	}

	.consequence-detail > div {
		margin-bottom: 1rem;
	}

	.consequence-detail > div:last-child {
		margin-bottom: 0;
	}

	.consequence-detail strong {
		color: #1f2937;
		display: block;
		margin-bottom: 0.25rem;
	}

	.consequence-detail p {
		margin: 0;
		color: #1f2937;
		line-height: 1.6;
		word-wrap: break-word;
		overflow-wrap: break-word;
	}

	.script-box {
		background: #fff9e6;
		border-left: 4px solid #f39c12;
		padding: 1.5rem;
		border-radius: 1rem;
		margin-bottom: 1.5rem;
	}

	.script-box h3 {
		margin-top: 0;
		color: #1f2937;
		margin-bottom: 1rem;
	}

	.script-text {
		font-size: 1.05rem;
		line-height: 1.8;
		color: #1f2937;
		font-style: italic;
		margin-bottom: 1rem;
		word-wrap: break-word;
		overflow-wrap: break-word;
	}

	.copy-button {
		background: #f39c12;
		color: white;
		border: none;
		padding: 0.5rem 1rem;
		border-radius: 6px;
		cursor: pointer;
		font-size: 0.9rem;
		transition: background 0.3s;
	}

	.copy-button:hover {
		background: #e67e22;
	}

	.avoid-box {
		background: #ffebee;
		border-left: 4px solid #e74c3c;
		padding: 1.5rem;
		border-radius: 1rem;
		margin-bottom: 1.5rem;
	}

	.avoid-box h3 {
		margin-top: 0;
		color: #1f2937;
		margin-bottom: 1rem;
	}

	.avoid-box ul {
		margin: 0;
		padding-left: 1.5rem;
	}

	.avoid-box li {
		color: #1f2937;
		line-height: 1.8;
		margin-bottom: 0.5rem;
	}

	.principles-box {
		background: #e8f4f8;
		border-left: 4px solid #3b82f6;
		padding: 1.5rem;
		border-radius: 1rem;
	}

	.principles-box h3 {
		margin-top: 0;
		color: #1f2937;
		margin-bottom: 1rem;
	}

	.principles-box ul {
		margin: 0;
		padding-left: 1.5rem;
	}

	.principles-box li {
		color: #1f2937;
		line-height: 1.8;
		margin-bottom: 0.5rem;
	}

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}

		.consequence-types {
			grid-template-columns: 1fr;
		}
	}
</style>
