<script lang="ts">
  import { onMount } from 'svelte';
  import MarkdownIt from 'markdown-it';

  export let categoryId: string;
  export let slug: string;

  let mdHtml: string | null = null;
  let loading = true;
  let error: string | null = null;
  let articleEl: HTMLElement | null = null;

  // import all markdown files as raw
  const modules = import.meta.glob('/src/content/**/*.md', { as: 'raw' });

  onMount(async () => {
    loading = true;
    error = null;
    mdHtml = null;
    try {
      const matchKey = Object.keys(modules).find(k => k.includes(`/${categoryId}/`) && k.endsWith(`${slug}.md`));
      if (!matchKey) {
        throw new Error('Article not found');
      }
      const loader = modules[matchKey] as () => Promise<string>;
      const raw = await loader();
      const md = new MarkdownIt({ html: true });
      mdHtml = md.render(raw);
    } catch (err: any) {
      error = err?.message ?? String(err);
    } finally {
      loading = false;
    }
  });
</script>

<div class="article-page">
  <a class="back" href="#/">← All categories</a>
  {#if loading}
    <p>Loading article…</p>
  {:else if error}
      <p class="error">{error}</p>
  {:else}
    <article class="md-content" bind:this={articleEl}>
      {@html mdHtml}
    </article>
  {/if}
</div>

<style>
  /* page wrapper centers the content on a soft gray background */
  .article-page {
    max-width: 760px;
    margin: 1.5rem auto;
    padding: 1.25rem;
    background: #ffffff; /* white article background */
    border-radius: 10px;
    box-shadow: 0 6px 18px rgba(21, 27, 36, 0.06);
  }

  .back {
    display: inline-block;
    margin: 0 0 1rem 0;
    color: #0b69ff;
    text-decoration: none;
    font-weight: 600;
    font-size: 0.95rem;
  }

  .md-content {
    padding: 0.25rem 0;
    color: #111827;
    line-height: 1.7;
    font-size: 1rem;
  }

  /* use :global for markdown-generated tags so Svelte doesn't mark selectors as unused */
  .md-content :global(h1),
  .md-content :global(h2),
  .md-content :global(h3) { color: #0f172a; margin-top: 1.25rem; margin-bottom: 0.5rem }

  .md-content :global(p) { margin: 0 0 1rem 0 }

  .md-content :global(img) { max-width: 100%; height: auto; border-radius: 6px }

  .error { color: #b00020 }
</style>
