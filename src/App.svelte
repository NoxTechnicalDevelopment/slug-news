<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import Category from './lib/Category.svelte';
  import ArticlePage from './lib/ArticlePage.svelte';

  type Article = { title: string; summary: string; url?: string; slug?: string };
  type CategoryType = { id: string; name: string; articles: Article[] };

  // Sample data: 5 categories, 2 articles each
  const categories: CategoryType[] = [
    {
      id: 'food',
      name: 'Food',
      articles: [
        { title: 'The Best Dining Hall at UCSC and Why It May Be Changing', summary: 'Cowell remains a student favorite, but other dining halls are adopting features like all-day pizza and expanded menus. New dining leadership aims to improve equity across colleges, narrowing the quality gap.', slug: 'article1' },
        { title: 'UCSC Strikes: How to Eat During a Union Strike', summary: 'Practical tips and strategies for students to access meals and cope with disruptions during a campus union strike.', slug: 'article2' }
      ]
    },
    {
      id: 'clubs',
      name: 'Clubs',
      articles: [
        { title: 'AI Startups Raise Record Funding', summary: 'Investors pour capital into generative models and applications.', slug: 'article1' },
        { title: 'Quantum Breakthrough', summary: 'New qubit technique improves coherence times in lab demonstrations.', slug: 'article2' }
      ]
    },
    {
      id: 'performing-arts',
      name: 'Performing Arts',
      articles: [
        { title: 'Los Mejicas: UCSC’s Baile Folklorico Legacy', summary: 'Los Mejicas, founded in 1972, preserves Mexican folkloric dance at UCSC while navigating funding and identity challenges to sustain its cultural legacy.', slug: 'article1' },
        { title: 'Rainbow Theater: A Student-Driven Multicultural Stage', summary: 'Founded in 1993, Rainbow Theater centers student-created multicultural productions and recently gained departmental recognition to expand its reach and resources.', slug: 'article2' }
      ]
    },
    {
      id: 'sports',
      name: 'Sports',
      articles: [
        { title: 'Sports Community at UCSC', summary: "If you're a student at UCSC you pay for sports, but do you get what you pay for.", slug: 'article1' },
        { title: 'College Athletes: Health, Protections, and NIL Challenges', summary: 'Our student athletes have historically not been taken care of, and often face harsh futures and little representation for compensation.', slug: 'article2' }
      ]
    },
    {
      id: 'gaming',
      name: 'Gaming',
      articles: [
        { title: 'Student Club Releases New Game', summary: 'A student club has released a newly developed game — highlighting student development, features, and the campus game-making community.', slug: 'article1' },
        { title: 'UCSC Team Creates Wildfire Prep Games', summary: 'A UCSC team developed interactive games to educate about wildfire risks and preparedness, connecting climate-driven wildfire trends to practical safety steps.', slug: 'article2' }
      ]
    }
  ];

  // Simple hash-based routing: #/categoryId => show that category
    const parseHashParts = () => {
      const path = (window.location.hash || '').replace(/^#\/?/, '');
      const parts = path.split('/').filter(Boolean);
      return { categoryId: parts[0] ?? null, slug: parts[1] ?? null };
    };

    let activeCategoryId: string | null = null;
    let activeArticleSlug: string | null = null;

    let activeCategory: CategoryType | null = null;

    $: activeCategory = categories.find(c => c.id === activeCategoryId) ?? null;

    const onHashChange = () => {
      const { categoryId, slug } = parseHashParts();
      activeCategoryId = categoryId || null;
      activeArticleSlug = slug || null;
    };

    onMount(() => {
      onHashChange();
      window.addEventListener('hashchange', onHashChange);
    });

    onDestroy(() => window.removeEventListener('hashchange', onHashChange));
</script>

<main class="news-site">
  <header class="site-header">
    <div class="brand">
      <h1>Slug News</h1>
    </div>
    <nav class="top-nav">
      {#each categories as c}
        <a href={'#/' + c.id} class="nav-link {c.id === activeCategoryId ? 'active' : ''}">{c.name}</a>
      {/each}
    </nav>
  </header>

  {#if activeArticleSlug && activeCategory}
    <ArticlePage categoryId={activeCategory.id} slug={activeArticleSlug} />
  {:else if activeCategoryId}
    {#if activeCategory}
      <div class="category-page">
        <a class="back" href="#/">← All categories</a>
        <Category category={activeCategory} />
      </div>
    {:else}
      <div class="not-found">
        <a class="back" href="#/">← All categories</a>
        <p>Category not found.</p>
      </div>
    {/if}
  {:else}
    <section class="categories-grid">
      {#each categories as category}
        <Category {category} />
      {/each}
    </section>
  {/if}

  <footer class="site-footer">© {new Date().getFullYear()} Slug News — Demo content</footer>
</main>

<style>
  .news-site {
    font-family: Inter, ui-sans-serif, system-ui, -apple-system, 'Segoe UI', Roboto, 'Helvetica Neue', Arial;
    padding: 2rem 1.25rem;
    color: #0f172a;
    max-width: 1100px;
    margin: 0 auto;
  }
  .site-header {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
    align-items: start;
    margin-bottom: 1rem;
  }
  .brand {
    display: flex;
    align-items: center;
    gap: 0.75rem;
  }
  .brand h1 {
    margin: 0;
    font-size: 1.25rem;
    font-weight: 700;
  }
  .logo {
    height: 2.5rem;
    will-change: filter;
    transition: filter 200ms;
  }
  .logo.svelte:hover { filter: drop-shadow(0 0 6px #ff3e00aa); }

  .top-nav {
    display: flex;
    gap: 0.75rem;
    flex-wrap: wrap;
  }
  .nav-link {
    text-decoration: none;
    color: #374151;
    font-weight: 600;
    padding: 0.35rem 0.6rem;
    border-radius: 8px;
    transition: background 160ms ease, color 160ms ease;
  }

.nav-link:hover { background: rgba(15,23,42,0.04); color: #0b69ff }
.nav-link.active { background: #0b69ff; color: #fff }

  .categories-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 1rem;
  }

:global(body) { background: #f6f8fb; margin: 0 }

.category-page { max-width: 760px; margin: 1.5rem auto }

  .site-footer {
    margin-top: 2rem;
    color: #666;
    font-size: 0.9rem;
  }
</style>
