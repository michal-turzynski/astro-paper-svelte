<script lang="ts">
  import Fuse from "fuse.js";
  import Card2 from "./Card2.svelte";
  import slugify from "@utils/slugify";
  import type { Frontmatter } from "src/types";
  import { onMount } from "svelte";

  type SearchItem = {
    title: string;
    description: string;
    headings: string[];
    frontmatter: Frontmatter;
  };
  interface SearchResult {
    item: SearchItem;
    refIndex: number;
  }

  export let searchList: SearchItem[];
  $: inputVal = "";
  $: searchResults = [] as SearchResult[];

  const fuse = new Fuse(searchList, {
    keys: ["title", "description", "headings"],
    includeMatches: true,
    minMatchCharLength: 2,
    threshold: 0.5,
  });

  onMount(() => {
    // if URL has search query,
    // insert that search query in input field
    const searchUrl = new URLSearchParams(window.location.search);
    const searchStr = searchUrl.get("q");
    if (searchStr) {
      inputVal = searchStr;
      handleChange(inputVal);
    }
  });

  function handleChange(e: any) {
    inputVal = e.currentTarget.value;
    // Add search result only if
    // input value is more than one character
    console.log(e);
    console.log(inputVal);
    let inputResult = inputVal.length > 1 ? fuse.search(inputVal) : [];
    searchResults = inputResult;

    // Update search string in URL
    if (inputVal.length > 0) {
      const searchParams = new URLSearchParams(window.location.search);
      searchParams.set("q", inputVal);
      const newRelativePathQuery =
        window.location.pathname + "?" + searchParams.toString();
      window?.history?.pushState(null, "", newRelativePathQuery);
    } else {
      window?.history?.pushState(null, "", window.location.pathname);
    }
  }
</script>

<div>
  <label class="relative block">
    <span class="absolute inset-y-0 left-0 flex items-center pl-2 opacity-75">
      <svg xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
        <path
          d="M19.023 16.977a35.13 35.13 0 0 1-1.367-1.384c-.372-.378-.596-.653-.596-.653l-2.8-1.337A6.962 6.962 0 0 0 16 9c0-3.859-3.14-7-7-7S2 5.141 2 9s3.14 7 7 7c1.763 0 3.37-.66 4.603-1.739l1.337 2.8s.275.224.653.596c.387.363.896.854 1.384 1.367l1.358 1.392.604.646 2.121-2.121-.646-.604c-.379-.372-.885-.866-1.391-1.36zM9 14c-2.757 0-5-2.243-5-5s2.243-5 5-5 5 2.243 5 5-2.243 5-5 5z"
        />
      </svg>
    </span>
    <input
      class="placeholder:italic placeholder:text-opacity-75 py-3 pl-10 pr-3 
        block bg-skin-fill w-full rounded
        border border-skin-fill border-opacity-40 
        focus:outline-none focus:border-skin-accent"
      placeholder="Search for anything..."
      type="text"
      name="search"
      autoComplete="off"
      autoFocus
      on:input={handleChange}
      bind:value={inputVal}
    />
  </label>

  {#if inputVal.length > 1}
    <div class="mt-8">
      Found {searchResults?.length}
      {searchResults?.length && searchResults?.length === 1
        ? " result"
        : " results"}{" "}
      for '{inputVal}'
    </div>
  {/if}

  <ul>
    {#if searchResults}
      {#each searchResults as result}
        <Card2
          post={result.item.frontmatter}
          href={`/posts/${slugify(result.item.frontmatter)}`}
        />
      {/each}
    {/if}
  </ul>
</div>
