<script>
	import { goto } from '$app/navigation';

	let selectedCategory = $state(null);
	let selectedScript = $state(null);

	const scripts = {
		bedtime: {
			title: 'Bedtime & Sleep',
			icon: '🌙',
			situations: [
				{
					name: 'Getting out of bed',
					script: `"I can see you're having trouble staying in bed. Your body needs sleep to grow big and strong. Let's practice taking deep breaths together. I'll stay here for 2 minutes, then it's time to close your eyes and rest. If you need me, I'll be right [location]. I love you."`,
					tips: [
						'Stay calm and use a gentle, quiet voice',
						'Be consistent with the routine every night',
						'Offer a lovey or comfort item',
						'Consider a night light if they\'re afraid of the dark'
					]
				},
				{
					name: 'Afraid of the dark',
					script: `"I understand you're feeling scared. The dark can feel different, but you are safe. Let's name 3 things you can see from your bed. Your [favorite stuffed animal] is here to keep you company. I'm right in the other room if you need me. You're so brave."`,
					tips: [
						'Validate their feelings without dismissing them',
						'Offer a nightlight or keep door slightly open',
						'Create a "bravery plan" together during the day',
						'Read books about nighttime and fears'
					]
				},
				{
					name: 'Stalling at bedtime',
					script: `"I know you want to keep playing, and bedtime can feel hard. Our bodies need rest to have energy for tomorrow. We can [activity] tomorrow, but right now it's time to sleep. Let's do our bedtime routine together: brush teeth, story, and songs. Which book would you like tonight?"`,
					tips: [
						'Give a 10-minute and 5-minute warning before bed',
						'Keep the bedtime routine consistent and predictable',
						'Offer limited choices to give them some control',
						'Stay firm but empathetic'
					]
				}
			]
		},
		aggression: {
			title: 'Hitting, Biting & Aggression',
			icon: '✋',
			situations: [
				{
					name: 'Hitting a sibling',
					script: `"I won't let you hit. Hitting hurts. I can see you're feeling [angry/frustrated/upset]. Use your words to tell [sibling's name] how you feel. You can say 'I don't like that' or 'Stop please.' Let's take some deep breaths together. If you need to hit something, you can hit this pillow/cushion."`,
					tips: [
						'Stop the behavior immediately and calmly',
						'Separate the children if needed',
						'Help them identify their emotion',
						'Teach alternative ways to express feelings',
						'Check on both children'
					]
				},
				{
					name: 'Biting',
					script: `"Biting hurts [person's name]. I won't let you bite people. I can see something is bothering you. Let's use our words. Can you show me with your hands what you need?" [If teething]: "If your mouth hurts, you can bite this teether/toy, not people."`,
					tips: [
						'Respond immediately but calmly',
						'Tend to the bitten child first',
						'Offer appropriate things to bite if teething',
						'Look for patterns (tiredness, overstimulation)',
						'Teach gentle touch'
					]
				},
				{
					name: 'Throwing toys',
					script: `"Toys are not for throwing. Throwing toys can break them and hurt people. I can see you want to throw! Let's go outside and throw [balls/beanbags] together, or you can throw these soft balls in the house. If you throw toys again, I'll need to put them away for now."`,
					tips: [
						'Remove the toy calmly if throwing continues',
						'Provide appropriate throwing activities',
						'Acknowledge the need to throw',
						'Follow through with consequences consistently'
					]
				}
			]
		},
		sharing: {
			title: 'Sharing & Turn-Taking',
			icon: '🤝',
			situations: [
				{
					name: 'Not taking turns',
					script: `"You both want the [toy]. That's hard! [Child 1], you can have it for 3 more minutes, then it's [Child 2]'s turn. Let's set a timer together so it's fair. [Child 2], while you're waiting, would you like to [alternative activity]? I'll make sure you both get a turn."`,
					tips: [
						'Use a visual timer they can see',
						'Acknowledge both children\'s feelings',
						'Offer an alternative activity for the waiting child',
						'Praise turn-taking when it happens',
						'Stay neutral and calm'
					]
				},
				{
					name: 'Refusing to share',
					script: `"I know you're playing with that and don't want to share right now. That's okay - you can finish your turn. When you're done, it will be [other child]'s turn. [Other child], I know waiting is hard. Let's find something else fun to play with while [child] finishes."`,
					tips: [
						'Respect that they don\'t have to share everything',
						'Teach that turns eventually end',
						'Set clear expectations before playdates',
						'Allow them to put away special toys before friends come',
						'Model sharing yourself'
					]
				},
				{
					name: 'Grabbing toys from others',
					script: `"I can see you really want that toy, but [child] is using it right now. Grabbing is not okay. We can ask: 'Can I have a turn please?' If they say no, we wait or find something else. Let's practice asking together. What else looks fun to play with?"`,
					tips: [
						'Stop the grabbing immediately',
						'Model asking politely',
						'Teach them to accept "no" as an answer',
						'Help them find alternatives',
						'Praise gentle requests'
					]
				}
			]
		},
		tantrums: {
			title: 'Tantrums & Big Feelings',
			icon: '😭',
			situations: [
				{
					name: 'Public meltdown',
					script: `[Quietly and calmly]: "I can see you're very upset. I'm right here with you. When you're ready, we can [take deep breaths/get a hug/talk about it]. I'll keep you safe." [If needed]: "We're going to [quiet space] until you feel calmer."`,
					tips: [
						'Stay calm yourself - your regulation helps them regulate',
						'Move to a quieter space if possible',
						'Ignore stares from others',
						'Keep them safe but give space if they need it',
						'Don\'t try to reason during the peak of the tantrum',
						'Talk about it later when everyone is calm'
					]
				},
				{
					name: 'Screaming and yelling',
					script: `"I hear you're very upset. Screaming hurts my ears. When you're ready to use your normal voice, I'm ready to listen. I'll be right here." [Then give them space while staying nearby]`,
					tips: [
						'Don\'t yell back',
						'Wait for them to calm down',
						'Offer comfort if they want it',
						'Set a boundary about screaming',
						'Address the issue once calm'
					]
				},
				{
					name: 'Won\'t calm down',
					script: `"You're having very big feelings right now. Your body needs help calming down. Let's try [deep breaths/counting/getting a drink of water/squeezing hands]. I'm here to help you. You're safe."`,
					tips: [
						'Co-regulate with them',
						'Try different calming strategies',
						'Physical touch if they want it (hug, hand on back)',
						'Change the environment if needed',
						'Be patient - big feelings take time to process'
					]
				}
			]
		},
		transitions: {
			title: 'Transitions & Changes',
			icon: '🔄',
			situations: [
				{
					name: 'Refusing to leave playground',
					script: `"I know you're having so much fun and it's hard to leave. We have 5 more minutes, then it's time to go. Let's do your favorite thing one more time. What would you like to do? After we leave, we're going to [next activity]. You can come back and play again [when]."`,
					tips: [
						'Give warnings: 10 minutes, 5 minutes, 1 minute',
						'Acknowledge their feelings',
						'Offer a choice of last activity',
						'Preview what\'s coming next',
						'Stay firm but empathetic',
						'Bring a comfort item for the car'
					]
				},
				{
					name: 'Morning routine resistance',
					script: `"I know mornings can feel hard. Our body is still waking up! Let's look at our chart together and see what's next. Do you want to [choice A] or [choice B] first? I'll help you. We can make it fun - want to race to see who can get dressed faster?"`,
					tips: [
						'Create a visual morning routine chart',
						'Offer limited choices',
						'Make it playful when possible',
						'Prepare items the night before',
						'Wake them with enough time to move slowly',
						'Positive reinforcement for cooperation'
					]
				},
				{
					name: 'Stopping screen time',
					script: `"You're watching [show/game] and it's almost time to stop. You have 5 more minutes. When the timer goes off, we'll turn it off together. Then we're going to [next activity]. I know stopping can feel hard, but we can play/watch again tomorrow."`,
					tips: [
						'Set clear limits before screen time starts',
						'Use a timer they can see',
						'Give multiple warnings',
						'Acknowledge disappointment',
						'Have a transition activity ready',
						'Be consistent with limits'
					]
				}
			]
		},
		defiance: {
			title: 'Defiance & Not Listening',
			icon: '🙅',
			situations: [
				{
					name: 'Saying "No!" to everything',
					script: `"I hear you saying no. You want to be in charge of your body - that's good! But [action] needs to happen for [reason]. You can choose: do you want to [option A] or [option B]? I'll give you until I count to 3 to choose, then I'll choose for you."`,
					tips: [
						'Offer limited choices',
						'Stay calm and matter-of-fact',
						'Follow through consistently',
						'Pick your battles',
						'Give them control where you can',
						'Acknowledge their autonomy'
					]
				},
				{
					name: 'Ignoring instructions',
					script: `[Get down to their level, make eye contact]: "I need you to [specific instruction]. I'm going to wait right here while you do it. Do you need help or can you do it yourself?" [Wait patiently and repeat once if needed]`,
					tips: [
						'Get their attention first before giving instructions',
						'Use clear, simple language',
						'Give one instruction at a time',
						'Wait for compliance',
						'Offer help if genuinely needed',
						'Praise when they listen'
					]
				},
				{
					name: 'Testing boundaries',
					script: `"I know you want to see what will happen, but the rule is [rule]. That doesn't change. If you [behavior] again, then [consequence] will happen. I know you can make a good choice."`,
					tips: [
						'Be consistent with rules',
						'Follow through every time',
						'Keep consequences logical and immediate',
						'Stay calm and unemotional',
						'Remind them of the rule before they break it'
					]
				}
			]
		}
	};

	function selectCategory(category) {
		selectedCategory = category;
		selectedScript = null;
	}

	function selectScript(script) {
		selectedScript = script;
	}

	function copyToClipboard(text) {
		navigator.clipboard.writeText(text).then(() => {
			alert('Script copied to clipboard!');
		});
	}
</script>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>Script Library</h1>
		<div style="width: 100px;"></div>
	</header>

	<div class="content">
		{#if !selectedCategory}
			<div class="intro">
				<p>
					Choose a category below to find helpful scripts for common parenting situations. These
					scripts are designed to help you respond calmly and effectively in challenging moments.
				</p>
			</div>

			<div class="categories-grid">
				{#each Object.entries(scripts) as [key, category]}
					<button class="category-card" onclick={() => selectCategory(key)}>
						<div class="category-icon">{category.icon}</div>
						<h2>{category.title}</h2>
						<p>{category.situations.length} scripts</p>
					</button>
				{/each}
			</div>
		{:else if !selectedScript}
			<div class="breadcrumb">
				<button onclick={() => (selectedCategory = null)}>← All Categories</button>
			</div>

			<h2 class="category-title">
				<span class="icon">{scripts[selectedCategory].icon}</span>
				{scripts[selectedCategory].title}
			</h2>

			<div class="situations-list">
				{#each scripts[selectedCategory].situations as situation}
					<button class="situation-card" onclick={() => selectScript(situation)}>
						<h3>{situation.name}</h3>
						<span class="arrow">→</span>
					</button>
				{/each}
			</div>
		{:else}
			<div class="breadcrumb">
				<button onclick={() => (selectedScript = null)}>
					← {scripts[selectedCategory].title}
				</button>
			</div>

			<div class="script-detail">
				<h2>{selectedScript.name}</h2>

				<div class="script-box">
					<h3>What to Say:</h3>
					<div class="script-text">{selectedScript.script}</div>
					<button class="copy-button" onclick={() => copyToClipboard(selectedScript.script)}>
						📋 Copy Script
					</button>
				</div>

				<div class="tips-box">
					<h3>Tips & Reminders:</h3>
					<ul>
						{#each selectedScript.tips as tip}
							<li>{tip}</li>
						{/each}
					</ul>
				</div>

				<div class="reminder">
					<strong>Remember:</strong> Every child is different. Adapt these scripts to fit your child's
					age, personality, and your family's values. The most important thing is staying calm and connected.
				</div>
			</div>
		{/if}
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

	.intro {
		background: #e8f4f8;
		padding: 1.5rem;
		border-radius: 8px;
		margin-bottom: 2rem;
		color: #2c3e50;
		line-height: 1.6;
	}

	.categories-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 1.5rem;
	}

	.category-card {
		background: white;
		border: 2px solid #e0e0e0;
		border-radius: 12px;
		padding: 2rem;
		text-align: center;
		cursor: pointer;
		transition: all 0.3s;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.category-card:hover {
		transform: translateY(-4px);
		box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
		border-color: #3498db;
	}

	.category-icon {
		font-size: 4rem;
		margin-bottom: 1rem;
	}

	.category-card h2 {
		font-size: 1.3rem;
		color: #2c3e50;
		margin-bottom: 0.5rem;
	}

	.category-card p {
		color: #7f8c8d;
		font-size: 0.9rem;
	}

	.breadcrumb {
		margin-bottom: 1.5rem;
	}

	.breadcrumb button {
		background: #ecf0f1;
		border: none;
		padding: 0.5rem 1rem;
		border-radius: 6px;
		cursor: pointer;
		color: #2c3e50;
		font-size: 0.95rem;
		transition: background 0.3s;
	}

	.breadcrumb button:hover {
		background: #bdc3c7;
	}

	.category-title {
		display: flex;
		align-items: center;
		gap: 1rem;
		font-size: 2rem;
		color: #2c3e50;
		margin-bottom: 2rem;
	}

	.category-title .icon {
		font-size: 3rem;
	}

	.situations-list {
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}

	.situation-card {
		background: white;
		border: 2px solid #e0e0e0;
		border-radius: 8px;
		padding: 1.5rem;
		cursor: pointer;
		transition: all 0.3s;
		display: flex;
		justify-content: space-between;
		align-items: center;
		text-align: left;
	}

	.situation-card:hover {
		border-color: #3498db;
		transform: translateX(4px);
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.situation-card h3 {
		font-size: 1.2rem;
		color: #2c3e50;
		margin: 0;
	}

	.situation-card .arrow {
		font-size: 1.5rem;
		color: #3498db;
	}

	.script-detail {
		background: white;
		border-radius: 12px;
		padding: 2rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.script-detail h2 {
		color: #2c3e50;
		font-size: 2rem;
		margin-bottom: 1.5rem;
	}

	.script-box {
		background: #e8f4f8;
		border-left: 4px solid #3498db;
		padding: 1.5rem;
		border-radius: 8px;
		margin-bottom: 2rem;
	}

	.script-box h3 {
		color: #2c3e50;
		margin-top: 0;
		margin-bottom: 1rem;
	}

	.script-text {
		font-size: 1.1rem;
		line-height: 1.8;
		color: #2c3e50;
		font-style: italic;
		margin-bottom: 1rem;
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

	.tips-box {
		background: #fff9e6;
		border-left: 4px solid #f39c12;
		padding: 1.5rem;
		border-radius: 8px;
		margin-bottom: 1.5rem;
	}

	.tips-box h3 {
		color: #2c3e50;
		margin-top: 0;
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

	.reminder {
		background: #e8f8f5;
		border-left: 4px solid #27ae60;
		padding: 1rem;
		border-radius: 8px;
		color: #2c3e50;
		line-height: 1.6;
	}

	.reminder strong {
		color: #27ae60;
	}

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}

		.script-detail {
			padding: 1rem;
		}

		.category-title {
			font-size: 1.5rem;
		}
	}
</style>
