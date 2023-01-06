<script lang="ts">
  import type { Frontmatter } from "src/types";
  import Datetime from "./DateTime2.svelte";

  export let href: string;
  export let post: Frontmatter;
  export let secHeading: boolean | null = true;

  const styles = {
    cardContainer: "my-6",
    titleLink:
      "text-skin-accent font-medium text-lg underline-offset-4 decoration-dashed focus-visible:no-underline focus-visible:underline-offset-0 inline-block",
    titleHeading: "font-medium text-lg decoration-dashed hover:underline",
  };
</script>

<li class={styles.cardContainer}>
  <a {href} class={styles.titleLink}>
    {#if secHeading}
      <h2 class={styles.titleHeading}>
        {post.title}
      </h2>
    {:else}
      <h3 class={styles.titleHeading}>
        {post.title}
      </h3>
    {/if}
  </a>
  <Datetime datetime={post.datetime} />
  <p>{post.description}</p>
</li>
