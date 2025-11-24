<script lang="ts">
  export let article: { title: string; summary: string; url?: string; slug?: string };
  export let categoryId: string | undefined;
</script>

<article class="article-card">
  <h3>{article.title}</h3>
  <p class="summary">{article.summary}</p>
  {#if article.url}
    <a class="read-more" href={article.url} target="_blank" rel="noreferrer">Read more</a>
  {:else if article.slug && categoryId}
    <a class="read-more" href={'#/' + categoryId + '/' + article.slug}>Read more</a>
  {/if}
</article>

<style>
  .article-card {
    padding: 0.75rem 0.9rem;
    border-radius: 10px;
    background: #ffffff;
    border: 1px solid #eceff4;
    transition: transform 180ms ease, box-shadow 180ms ease;
    will-change: transform, box-shadow;
  }
  .article-card h3 {
    margin: 0 0 0.25rem 0;
    font-size: 1rem;
  }
  .summary {
    margin: 0 0 0.5rem 0;
    color: #444;
    font-size: 0.95rem;
  }
  .read-more {
    color: #0366d6;
    font-weight: 600;
    text-decoration: none;
    font-size: 0.9rem;
  }

.article-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(16,24,40,0.06);
}
</style>
