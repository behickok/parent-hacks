<script>
	import { goto } from '$app/navigation';

	let selectedCategory = $state(null);

	const resources = {
		sleep: {
			title: 'Sleep & Bedtime',
			icon: '😴',
			color: '#9b59b6',
			articles: [
				{
					title: 'Sleeping Through the Night',
					source: 'KellyMom',
					url: 'https://kellymom.com/parenting/nighttime/sleep/',
					description: 'Understanding infant sleep patterns and realistic expectations for different ages.'
				},
				{
					title: 'The Practical Magic of Daily Routines',
					source: 'Zero to Three',
					url: 'https://www.zerotothree.org/resource/the-practical-magic-of-daily-routines/',
					description: 'Creating consistent, calming bedtime routines that work.'
				},
				{
					title: 'Safe Sleep Guidelines',
					source: 'AAP (American Academy of Pediatrics)',
					url: 'https://www.healthychildren.org/English/ages-stages/baby/sleep/Pages/A-Parents-Guide-to-Safe-Sleep.aspx',
					description: 'Evidence-based information on safe sleep practices and SIDS prevention.'
				},
				{
					title: 'Studies on Normal Infant Sleep',
					source: 'KellyMom',
					url: 'https://kellymom.com/parenting/nighttime/sleepstudies/',
					description: 'Understanding why babies and toddlers wake at night and how to respond.'
				}
			]
		},
		behavior: {
			title: 'Behavior & Discipline',
			icon: '🎯',
			color: '#e74c3c',
			articles: [
				{
					title: 'Positive Discipline Strategies',
					source: 'Positive Discipline',
					url: 'https://www.positivediscipline.com/',
					description: 'Respectful parenting approaches that teach life skills without punishment.'
				},
				{
					title: 'Toddler Tantrums 101',
					source: 'Zero to Three',
					url: 'https://www.zerotothree.org/resource/toddler-tantrums-101-why-they-happen-and-what-you-can-do/',
					description: 'Why tantrums happen and how to handle them with empathy.'
				},
				{
					title: 'Setting Limits With Toddlers',
					source: 'Janet Lansbury',
					url: 'https://www.janetlansbury.com/2012/05/setting-limits-with-toddlers-the-choices-they-cant-make/',
					description: 'Setting firm, respectful limits while maintaining connection.'
				},
				{
					title: 'The Whole-Brain Child',
					source: 'Dr. Daniel Siegel',
					url: 'https://drdansiegel.com/book/the-whole-brain-child/',
					description: 'Using brain science to navigate emotional challenges and discipline.'
				}
			]
		},
		development: {
			title: 'Child Development',
			icon: '🌱',
			color: '#27ae60',
			articles: [
				{
					title: 'CDC Developmental Milestones',
					source: 'CDC',
					url: 'https://www.cdc.gov/act-early/milestones/index.html',
					description: 'Track your child\'s development from birth to 5 years with free milestone tracker.'
				},
				{
					title: 'Play and Learning Go Together',
					source: 'NAEYC',
					url: 'https://www.naeyc.org/our-work/families/play-learning-go-together',
					description: 'How children learn through play at different ages.'
				},
				{
					title: 'Brain Architecture',
					source: 'Harvard Center on the Developing Child',
					url: 'https://developingchild.harvard.edu/key-concept/brain-architecture/',
					description: 'The science of early brain development and what it means for parents.'
				},
				{
					title: 'Creating Routines for Love and Learning',
					source: 'Zero to Three',
					url: 'https://www.zerotothree.org/resource/creating-routines-for-love-and-learning/',
					description: 'Supporting emotional development through consistent, loving routines.'
				}
			]
		},
		connection: {
			title: 'Connection & Attachment',
			icon: '❤️',
			color: '#e91e63',
			articles: [
				{
					title: 'Attachment Parenting Resources',
					source: 'Attachment Parenting International',
					url: 'https://attachmentparenting.org/',
					description: 'Understanding secure attachment and responsive parenting.'
				},
				{
					title: 'Special Time: Building Connection in Minutes',
					source: 'Hand in Hand Parenting',
					url: 'https://www.handinhandparenting.org/2016/08/listen-launch-post-special-time/',
					description: 'The power of one-on-one connection time.'
				},
				{
					title: 'Communication Tools for Parents',
					source: 'Dr. Laura Markham (Aha! Parenting)',
					url: 'https://www.ahaparenting.com/parenting-tools/communication',
					description: 'Repairing relationships and rebuilding connection.'
				},
				{
					title: 'Raising Kids Who Care',
					source: 'Greater Good Science Center',
					url: 'https://greatergood.berkeley.edu/topic/kids',
					description: 'Raising emotionally intelligent and compassionate children.'
				}
			]
		},
		regulation: {
			title: 'Emotional Regulation',
			icon: '🧘',
			color: '#3498db',
			articles: [
				{
					title: 'Circle of Security',
					source: 'Circle of Security International',
					url: 'https://www.circleofsecurityinternational.com/',
					description: 'How parents help children learn to regulate emotions through secure relationships.'
				},
				{
					title: 'Calming Strategies for Kids',
					source: 'Child Mind Institute',
					url: 'https://childmind.org/article/calming-strategies-kids/',
					description: 'Teaching children practical self-calming techniques.'
				},
				{
					title: 'Good Inside: Parenting Strategies',
					source: 'Dr. Becky Kennedy',
					url: 'https://www.goodinside.com/',
					description: 'Practical strategies for helping kids with overwhelming feelings.'
				},
				{
					title: 'Whole-Brain Child Handouts',
					source: 'Dr. Dan Siegel & Dr. Tina Payne Bryson',
					url: 'https://drdansiegel.com/whole-brain-child-handouts/',
					description: 'Free resources using brain science to navigate emotional challenges.'
				}
			]
		},
		selfcare: {
			title: 'Parent Self-Care',
			icon: '🌟',
			color: '#f39c12',
			articles: [
				{
					title: 'Parenting Basics',
					source: 'Psychology Today',
					url: 'https://www.psychologytoday.com/us/basics/parenting',
					description: 'Understanding parental stress and recognizing when to seek support.'
				},
				{
					title: 'Self-Compassion for Parents',
					source: 'Dr. Kristin Neff',
					url: 'https://self-compassion.org/category/children-teens-and-parents/',
					description: 'Being kind to yourself in the parenting journey.'
				},
				{
					title: 'Perinatal Mental Health Support',
					source: 'Postpartum Support International',
					url: 'https://www.postpartum.net/',
					description: 'Resources for parental mental health and support.'
				},
				{
					title: 'Mindful Parenting',
					source: 'Mindful Magazine',
					url: 'https://www.mindful.org/mindful-parenting/',
					description: 'Practical mindfulness and self-care ideas for busy parents.'
				}
			]
		}
	};

	function selectCategory(category) {
		selectedCategory = category;
	}
</script>

<div class="container">
	<header>
		<button class="back-button" onclick={() => goto('/')}>← Back</button>
		<h1>Resources</h1>
		<div style="width: 100px;"></div>
	</header>

	{#if !selectedCategory}
		<div class="intro">
			<p>
				A curated collection of evidence-based articles, guides, and resources from trusted
				parenting experts and organizations. Click on any category to explore helpful resources.
			</p>
		</div>

		<div class="categories-grid">
			{#each Object.entries(resources) as [key, category]}
				<button
					class="category-card"
					style="border-color: {category.color}"
					onclick={() => selectCategory(key)}
				>
					<div class="category-icon">{category.icon}</div>
					<h2>{category.title}</h2>
					<p>{category.articles.length} resources</p>
				</button>
			{/each}
		</div>
	{:else}
		<div class="breadcrumb">
			<button onclick={() => (selectedCategory = null)}>← All Categories</button>
		</div>

		<h2 class="category-title" style="color: {resources[selectedCategory].color}">
			<span class="icon">{resources[selectedCategory].icon}</span>
			{resources[selectedCategory].title}
		</h2>

		<div class="articles-list">
			{#each resources[selectedCategory].articles as article}
				<div class="article-card">
					<div class="article-header">
						<h3>{article.title}</h3>
						<span class="source">{article.source}</span>
					</div>
					<p class="description">{article.description}</p>
					<a
						href={article.url}
						target="_blank"
						rel="noopener noreferrer"
						class="read-more"
						style="background: {resources[selectedCategory].color}"
					>
						Read Article →
					</a>
				</div>
			{/each}
		</div>

		<div class="disclaimer">
			<strong>Note:</strong> These resources are provided for educational purposes. Always consult
			with your pediatrician or a qualified professional for specific concerns about your child.
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
		background: #e8f4f8;
		padding: 1.5rem;
		border-radius: 8px;
		margin-bottom: 2rem;
		color: #2c3e50;
		line-height: 1.6;
		text-align: center;
	}

	.categories-grid {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
		gap: 1.5rem;
	}

	.category-card {
		background: white;
		border: 3px solid;
		border-radius: 12px;
		padding: 2rem;
		text-align: center;
		cursor: pointer;
		transition: all 0.3s;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.category-card:hover {
		transform: translateY(-4px);
		box-shadow: 0 6px 16px rgba(0, 0, 0, 0.15);
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
		margin-bottom: 2rem;
	}

	.category-title .icon {
		font-size: 3rem;
	}

	.articles-list {
		display: flex;
		flex-direction: column;
		gap: 1.5rem;
		margin-bottom: 2rem;
	}

	.article-card {
		background: white;
		border: 2px solid #e0e0e0;
		border-radius: 12px;
		padding: 1.5rem;
		transition: all 0.3s;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
	}

	.article-card:hover {
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
		transform: translateY(-2px);
	}

	.article-header {
		display: flex;
		justify-content: space-between;
		align-items: flex-start;
		gap: 1rem;
		margin-bottom: 0.75rem;
		flex-wrap: wrap;
	}

	.article-card h3 {
		font-size: 1.3rem;
		color: #2c3e50;
		margin: 0;
		flex: 1;
	}

	.source {
		background: #ecf0f1;
		color: #2c3e50;
		padding: 0.25rem 0.75rem;
		border-radius: 12px;
		font-size: 0.85rem;
		font-weight: 600;
		white-space: nowrap;
	}

	.description {
		color: #7f8c8d;
		line-height: 1.6;
		margin-bottom: 1rem;
	}

	.read-more {
		display: inline-block;
		color: white;
		padding: 0.75rem 1.5rem;
		border-radius: 8px;
		text-decoration: none;
		font-weight: 600;
		transition: all 0.3s;
	}

	.read-more:hover {
		transform: translateX(4px);
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
	}

	.disclaimer {
		background: #fff3cd;
		border-left: 4px solid #f39c12;
		padding: 1rem;
		border-radius: 8px;
		color: #2c3e50;
		line-height: 1.6;
		font-size: 0.95rem;
	}

	.disclaimer strong {
		color: #f39c12;
	}

	@media (max-width: 600px) {
		.container {
			padding: 1rem;
		}

		.article-header {
			flex-direction: column;
			align-items: flex-start;
		}

		.category-title {
			font-size: 1.5rem;
		}
	}
</style>
