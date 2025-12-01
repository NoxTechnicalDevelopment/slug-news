<script lang="ts">
  export let article: { title: string; summary: string; url?: string; slug?: string };
  export let categoryId: string | undefined;
  export let compact: boolean = false;

  // compute the href for the whole-card link when possible
  let href: string | null = null;
  $: href = article.url
    ? article.url
    : article.slug && categoryId
    ? `#/${categoryId}/${article.slug}`
    : null;
</script>

{#if href}
  <a class="article-card card-link" href={href} target={article.url ? '_blank' : undefined} rel={article.url ? 'noreferrer noopener' : undefined} aria-label={article.title} class:compact={compact}>
    <h3>{article.title}</h3>
    <p class="summary {compact ? 'compact' : ''}">{article.summary}</p>
    {#if !compact}
      <span class="read-more">Read more</span>
    {/if}
  </a>
{:else}
  <article class="article-card" aria-label={article.title} class:compact={compact}>
    <h3>{article.title}</h3>
    <p class="summary {compact ? 'compact' : ''}">{article.summary}</p>
    <!-- no link available -->
  </article>
{/if}

<style>
  .article-card {
    padding: 1rem;
    border-radius: 10px;
    background: #ffffff;
    border: 1px solid #eceff4;
    transition: transform 180ms ease, box-shadow 180ms ease;
    will-change: transform, box-shadow;
  }
  .card-link {
    display: block;
    color: inherit;
    text-decoration: none;
  }
  .article-card h3 {
    margin: 0 0 0.5rem 0;
    font-size: 1.85rem;
    line-height: 1.15;
    font-weight: 700;
  }
  .article-card.compact h3 {
    font-size: 1.05rem;
    font-weight: 600;
    line-height: 1.25;
    margin: 0;
    color: rgba(15,23,42,0.85);
  }
  /* compact summary: truncated preview with fade */
  .summary.compact {
    max-height: 3.3rem; /* ~2 lines depending on font-size */
    overflow: hidden;
    position: relative;
    margin-bottom: 0.5rem;
    color: rgba(15,23,42,0.75);
    font-size: 0.98rem;
    line-height: 1.45;
  }
  .summary.compact::after {
    content: '';
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    height: 1.2rem;
    pointer-events: none;
    background: linear-gradient(180deg, rgba(255,255,255,0), #ffffff 60%);
  }
  .summary {
    margin: 0 0 0.9rem 0;
    color: #475569;
    font-size: 1.3rem;
    line-height: 1.6;
  }
  .read-more {
    color: #0366d6;
    font-weight: 600;
    text-decoration: none;
    font-size: 0.9rem;
  }

.card-link:focus {
  outline: 3px solid rgba(11,105,255,0.15);
  outline-offset: 4px;
}

.article-card:hover {
  box-shadow: 0 8px 24px rgba(16,24,40,0.06);
}
</style>
