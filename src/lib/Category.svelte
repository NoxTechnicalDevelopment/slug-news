<script lang="ts">
  import ArticleCard from './ArticleCard.svelte';
  type Article = { title: string; summary: string; url?: string; slug?: string };
  export let category: { id: string; name: string; articles: Article[] };
  export let compact: boolean = false;
</script>

<section class="category" id={category.id}>
  <h2>{category.name}</h2>
  <div class="articles">
    {#each category.articles as article (article.title)}
  <ArticleCard {article} categoryId={category.id} compact={compact} />
    {/each}
  </div>
</section>

<style>
  .category {
    background: #ffffff;
    border: 1px solid #eef2f6;
    border-radius: 10px;
    padding: 0.9rem;
    box-shadow: 0 6px 18px rgba(21, 27, 36, 0.04);
  }
  .category h2 {
    margin: 0 0 0.5rem 0;
    font-size: 1.1rem;
  }
  .articles {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }
</style>
