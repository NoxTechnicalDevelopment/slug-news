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
        { title: 'The Best Dining Hall at UCSC and Why it May be Changing.', summary: 'Leaders agree on new emissions targets and a framework for follow-up commitments.', slug: 'article1' },
        { title: 'UCSC Strikes: How to Eat During a Union Strike', summary: 'Key races reshape political maps across several countries.', slug: 'article2' }
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
        { title: 'Markets Rally', summary: 'Stocks climb after a string of positive economic indicators.', slug: 'article1' },
        { title: 'Retailers Shift Strategy', summary: 'Omnichannel experiences become central to growth plans.', slug: 'article2' }
      ]
    },
    {
      id: 'sports',
      name: 'Sports',
      articles: [
        { title: 'Championship Thriller', summary: 'A last-minute goal seals the championship in dramatic fashion.', slug: 'article1' },
        { title: 'Olympic Preparations', summary: 'Host city finalizes venues and schedules ahead of the games.', slug: 'article2' }
      ]
    },
    {
      id: 'gaming',
      name: 'Gaming',
      articles: [
        { title: 'Film Festival Highlights', summary: 'Indie films and breakout directors take the spotlight.', slug: 'article1' },
        { title: 'Art Auction Sets Records', summary: 'Contemporary works fetch unprecedented prices at auction.', slug: 'article2' }
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
