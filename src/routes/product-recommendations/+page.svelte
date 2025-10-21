<script>
	import { goto } from '$app/navigation';

	let selectedCategory = $state('all');

	const categories = [
		{ id: 'all', name: 'All Products', emoji: '🌟' },
		{ id: 'behavior', name: 'Behavior & Emotions', emoji: '🎭' },
		{ id: 'organization', name: 'Organization & Planning', emoji: '📋' },
		{ id: 'sensory', name: 'Sensory & Calm', emoji: '🧘' },
		{ id: 'learning', name: 'Learning & Development', emoji: '📚' },
		{ id: 'books', name: 'Parenting Books', emoji: '📖' }
	];

	const products = [
		{
			name: 'Visual Timer',
			category: 'behavior',
			description: 'Time Timer® visual countdown helps kids understand time remaining during activities and transitions.',
			reason: 'Perfect companion to our Transition Timer - provides physical visual aid that kids can see.',
			link: 'https://www.amazon.com/s?k=time+timer+visual',
			price: '$25-40'
		},
		{
			name: 'Feelings Poster Set',
			category: 'behavior',
			description: 'Colorful emotion charts with faces and labels to help children identify and express feelings.',
			reason: 'Enhances our Feelings Wheel by providing visual reference throughout the day.',
			link: 'https://www.amazon.com/s?k=feelings+emotions+poster+chart+kids',
			price: '$10-20'
		},
		{
			name: 'Chore Chart Magnets',
			category: 'organization',
			description: 'Reusable magnetic chore charts with task tiles and reward system.',
			reason: 'Physical version of our Chore Chart - great for younger kids who need tangible tracking.',
			link: 'https://www.amazon.com/s?k=magnetic+chore+chart+kids',
			price: '$15-30'
		},
		{
			name: 'Weighted Blanket',
			category: 'sensory',
			description: 'Calming weighted blanket provides gentle pressure for anxiety relief and better sleep.',
			reason: 'Excellent addition to our Calming Activities for sensory-seeking children.',
			link: 'https://www.amazon.com/s?k=weighted+blanket+kids',
			price: '$40-80'
		},
		{
			name: 'Noise-Canceling Headphones (Kids)',
			category: 'sensory',
			description: 'Kid-safe volume-limited headphones that block distracting sounds.',
			reason: 'Helps with focus during homework and calming down during overwhelming moments.',
			link: 'https://www.amazon.com/s?k=kids+headphones+noise+canceling',
			price: '$20-50'
		},
		{
			name: 'Fidget Tool Set',
			category: 'sensory',
			description: 'Variety pack of sensory fidgets (stress balls, tangles, putty) for focus and calm.',
			reason: 'Works with our Calming Activities - gives hands something to do during emotional moments.',
			link: 'https://www.amazon.com/s?k=sensory+fidget+toys+set',
			price: '$15-25'
		},
		{
			name: 'Dry Erase Routine Board',
			category: 'organization',
			description: 'Reusable morning/evening routine checklist board kids can check off each day.',
			reason: 'Physical complement to our Routine Builder - great for bathroom or bedroom wall.',
			link: 'https://www.amazon.com/s?k=kids+routine+chart+dry+erase',
			price: '$12-25'
		},
		{
			name: 'Social Stories Books',
			category: 'learning',
			description: 'Books that teach emotional regulation, sharing, and social skills through stories.',
			reason: 'Extends our Script Library with stories kids can read independently or with parents.',
			link: 'https://www.amazon.com/s?k=social+stories+books+kids',
			price: '$10-20 each'
		},
		{
			name: 'Reward Sticker Chart',
			category: 'behavior',
			description: 'Motivational sticker charts with fun themes and milestone rewards.',
			reason: 'Pairs perfectly with our Pom Pom Tracker for variety in positive reinforcement.',
			link: 'https://www.amazon.com/s?k=reward+sticker+chart+kids',
			price: '$8-15'
		},
		{
			name: 'Breathing Buddies Plush',
			category: 'sensory',
			description: 'Stuffed animals designed for breathing exercises - rises and falls with breath.',
			reason: 'Makes our Calming Activities breathing exercises more engaging for young children.',
			link: 'https://www.amazon.com/s?k=breathing+buddy+plush',
			price: '$15-30'
		},
		{
			name: 'Kids\' Journal',
			category: 'learning',
			description: 'Guided journal with prompts for emotions, gratitude, and daily reflection.',
			reason: 'Helps older kids process feelings identified in our Feelings Wheel.',
			link: 'https://www.amazon.com/s?k=kids+guided+journal+emotions',
			price: '$10-18'
		},
		{
			name: 'Calm Down Corner Kit',
			category: 'sensory',
			description: 'Complete kit with cushion, breathing ball, emotion cards, and calming activities.',
			reason: 'Creates dedicated physical space for using our Calming Activities tools.',
			link: 'https://www.amazon.com/s?k=calm+down+corner+kit',
			price: '$40-70'
		},
		{
			name: 'Family Calendar Board',
			category: 'organization',
			description: 'Large wall calendar with color-coded sections for each family member.',
			reason: 'Helps visualize routines and activities planned in our Routine Builder.',
			link: 'https://www.amazon.com/s?k=family+wall+calendar+board',
			price: '$20-40'
		},
		{
			name: 'Play Therapy Toys',
			category: 'behavior',
			description: 'Dollhouse, action figures, and puppets for acting out scenarios and emotions.',
			reason: 'Supports our Conflict Mediator - kids can role-play solutions with toys.',
			link: 'https://www.amazon.com/s?k=play+therapy+toys',
			price: '$25-60'
		},
		{
			name: 'Sand Timer Set',
			category: 'organization',
			description: 'Multiple timers (1, 3, 5, 10 min) for visual time management without screens.',
			reason: 'Screen-free alternative to our Visual Timer for quick tasks and transitions.',
			link: 'https://www.amazon.com/s?k=sand+timer+set+kids',
			price: '$12-20'
		},
		// Parenting Books
		{
			name: 'The Whole-Brain Child',
			category: 'books',
			description: 'By Daniel J. Siegel & Tina Payne Bryson - Revolutionary strategies to nurture your child\'s developing mind.',
			reason: 'Understanding brain development helps you use our emotional tools more effectively.',
			link: 'https://www.amazon.com/Whole-Brain-Child-Revolutionary-Strategies-Developing/dp/0553386697',
			price: '$11-17'
		},
		{
			name: 'How to Talk So Kids Will Listen',
			category: 'books',
			description: 'By Adele Faber & Elaine Mazlish - Practical, innovative ways to solve common communication problems.',
			reason: 'Perfect companion to our Script Library - provides the framework behind effective communication.',
			link: 'https://www.amazon.com/How-Talk-Kids-Will-Listen/dp/1451663889',
			price: '$11-18'
		},
		{
			name: 'No-Drama Discipline',
			category: 'books',
			description: 'By Daniel J. Siegel & Tina Payne Bryson - The whole-brain way to calm chaos and nurture developing minds.',
			reason: 'Explains the "why" behind natural consequences in our Consequences Guide.',
			link: 'https://www.amazon.com/No-Drama-Discipline-Whole-Brain-Nurture-Developing/dp/034554806X',
			price: '$12-18'
		},
		{
			name: 'The Explosive Child',
			category: 'books',
			description: 'By Ross W. Greene - A new approach for understanding and parenting easily frustrated children.',
			reason: 'Essential reading for using our Conflict Mediator and calming strategies effectively.',
			link: 'https://www.amazon.com/Explosive-Child-Understanding-Frustrated-Chronically/dp/0062270451',
			price: '$13-20'
		},
		{
			name: 'Peaceful Parent, Happy Kids',
			category: 'books',
			description: 'By Dr. Laura Markham - How to stop yelling and start connecting with practical tools for emotional regulation.',
			reason: 'The philosophy behind all our parenting tools - building connection while setting limits.',
			link: 'https://www.amazon.com/Peaceful-Parent-Happy-Kids-Connecting/dp/0399160280',
			price: '$13-19'
		},
		{
			name: 'Hunt, Gather, Parent',
			category: 'books',
			description: 'By Michaeleen Doucleff - What ancient cultures can teach us about raising children.',
			reason: 'Fresh perspective on routines, chores, and emotional development.',
			link: 'https://www.amazon.com/Hunt-Gather-Parent-Ancient-Cultures/dp/1982149671',
			price: '$15-28'
		},
		{
			name: 'Positive Discipline',
			category: 'books',
			description: 'By Jane Nelsen - The classic guide to helping children develop self-discipline and responsibility.',
			reason: 'The research foundation for our natural consequences and routine-building approaches.',
			link: 'https://www.amazon.com/Positive-Discipline-Jane-Nelsen-EdD/dp/0345487672',
			price: '$12-17'
		},
		{
			name: 'The Power of Showing Up',
			category: 'books',
			description: 'By Daniel J. Siegel & Tina Payne Bryson - How parental presence shapes who our kids become.',
			reason: 'Understanding attachment helps you use all our emotional tools with greater intention.',
			link: 'https://www.amazon.com/Power-Showing-Up-Parental-Presence/dp/0525622985',
			price: '$14-19'
		}
	];

	const filteredProducts = $derived(
		selectedCategory === 'all'
			? products
			: products.filter((p) => p.category === selectedCategory)
	);
</script>

<svelte:head>
	<title>Product Recommendations - Parent Hacks</title>
</svelte:head>

<div class="container">
	<button class="back-btn" onclick={() => goto('/')}>← Back to Home</button>

	<div class="header">
		<h1>🛍️ Helpful Products for Parents</h1>
		<p class="subtitle">
			Carefully selected tools and resources that complement our digital Parent Hacks suite
		</p>
	</div>

	<div class="category-filter">
		{#each categories as category}
			<button
				class="category-btn"
				class:active={selectedCategory === category.id}
				onclick={() => (selectedCategory = category.id)}
			>
				<span class="emoji">{category.emoji}</span>
				<span>{category.name}</span>
			</button>
		{/each}
	</div>

	<div class="products-grid">
		{#each filteredProducts as product}
			<div class="product-card">
				<div class="product-header">
					<h3>{product.name}</h3>
					<span class="price">{product.price}</span>
				</div>
				<p class="description">{product.description}</p>
				<div class="reason">
					<strong>💡 Why we recommend it:</strong>
					<p>{product.reason}</p>
				</div>
				<a href={product.link} target="_blank" rel="noopener noreferrer" class="shop-btn">
					🛒 Shop Now
				</a>
			</div>
		{/each}
	</div>

	<div class="disclaimer">
		<p>
			<strong>Note:</strong> These are general recommendations based on common parenting needs. We are
			not affiliated with specific brands, and prices are approximate. Always research products and
			consult with professionals for your child's specific needs.
		</p>
	</div>
</div>

<style>
	.container {
		max-width: 1200px;
		margin: 0 auto;
		padding: 2rem;
	}

	.back-btn {
		background: white;
		border: 2px solid #3498db;
		color: #3498db;
		padding: 0.5rem 1rem;
		border-radius: 8px;
		cursor: pointer;
		font-size: 1rem;
		margin-bottom: 1.5rem;
		transition: all 0.3s;
	}

	.back-btn:hover {
		background: #3498db;
		color: white;
	}

	.header {
		text-align: center;
		margin-bottom: 2rem;
	}

	h1 {
		color: #2c3e50;
		font-size: 2.5rem;
		margin-bottom: 0.5rem;
	}

	.subtitle {
		color: #7f8c8d;
		font-size: 1.1rem;
		max-width: 700px;
		margin: 0 auto;
	}

	.category-filter {
		display: flex;
		flex-wrap: wrap;
		gap: 0.75rem;
		justify-content: center;
		margin-bottom: 2rem;
	}

	.category-btn {
		background: white;
		border: 2px solid #e0e0e0;
		padding: 0.6rem 1.2rem;
		border-radius: 25px;
		cursor: pointer;
		font-size: 0.95rem;
		transition: all 0.3s;
		display: flex;
		align-items: center;
		gap: 0.5rem;
	}

	.category-btn:hover {
		border-color: #3498db;
		transform: translateY(-2px);
	}

	.category-btn.active {
		background: #3498db;
		color: white;
		border-color: #3498db;
	}

	.emoji {
		font-size: 1.2rem;
	}

	.products-grid {
		display: grid;
		grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
		gap: 1.5rem;
		margin-bottom: 2rem;
	}

	.product-card {
		background: white;
		border-radius: 12px;
		padding: 1.5rem;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
		transition: transform 0.3s, box-shadow 0.3s;
		display: flex;
		flex-direction: column;
	}

	.product-card:hover {
		transform: translateY(-5px);
		box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
	}

	.product-header {
		display: flex;
		justify-content: space-between;
		align-items: start;
		margin-bottom: 0.75rem;
		gap: 1rem;
	}

	h3 {
		color: #2c3e50;
		font-size: 1.3rem;
		margin: 0;
		flex: 1;
	}

	.price {
		background: #f1f8ff;
		color: #3498db;
		padding: 0.3rem 0.7rem;
		border-radius: 6px;
		font-weight: 600;
		font-size: 0.9rem;
		white-space: nowrap;
	}

	.description {
		color: #555;
		line-height: 1.6;
		margin-bottom: 1rem;
	}

	.reason {
		background: #f8f9fa;
		border-left: 3px solid #3498db;
		padding: 0.75rem;
		border-radius: 6px;
		margin-bottom: 1rem;
		flex: 1;
	}

	.reason strong {
		color: #2c3e50;
		display: block;
		margin-bottom: 0.5rem;
	}

	.reason p {
		color: #555;
		margin: 0;
		line-height: 1.5;
		font-size: 0.95rem;
	}

	.shop-btn {
		display: block;
		width: 100%;
		background: #27ae60;
		color: white;
		padding: 0.75rem 1.2rem;
		border-radius: 8px;
		text-decoration: none;
		text-align: center;
		transition: all 0.3s;
		font-weight: 600;
		font-size: 1rem;
		box-shadow: 0 2px 4px rgba(39, 174, 96, 0.2);
	}

	.shop-btn:hover {
		background: #229954;
		transform: translateY(-2px);
		box-shadow: 0 4px 8px rgba(39, 174, 96, 0.3);
	}

	.disclaimer {
		background: #fff9e6;
		border: 1px solid #f1c40f;
		border-radius: 8px;
		padding: 1.5rem;
		margin-top: 2rem;
	}

	.disclaimer p {
		margin: 0;
		color: #856404;
		line-height: 1.6;
	}

	@media (max-width: 768px) {
		.container {
			padding: 1rem;
		}

		h1 {
			font-size: 2rem;
		}

		.subtitle {
			font-size: 1rem;
		}

		.products-grid {
			grid-template-columns: 1fr;
		}

		.category-filter {
			gap: 0.5rem;
		}

		.category-btn {
			font-size: 0.85rem;
			padding: 0.5rem 0.9rem;
		}
	}

	@media (max-width: 600px) {
		h3 {
			font-size: 1.1rem;
		}

		.product-header {
			flex-direction: column;
			align-items: start;
			gap: 0.5rem;
		}
	}
</style>
