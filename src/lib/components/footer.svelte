<script lang="ts">
  import { siteConfig } from '$config/site';
  import { fly } from 'svelte/transition';
  import { postsShow } from '$stores/posts';
  import tippy from '$lib/actions/tippy';
  import { onMount } from 'svelte';
  import { Base64 } from 'js-base64';

  let className: any = undefined;
  export { className as class };

  const sinceYear = siteConfig.since ? siteConfig.since.toString() : undefined;
  const curYear = new Date().toJSON().substring(0, 4);
  const copyrightYear = `${sinceYear && sinceYear !== curYear ? `${sinceYear}-${curYear}` : `${curYear}`}`;

  let loaded = false;
  onMount(() => {
    loaded = true;
  });
</script>

{#if loaded}
  {#key $postsShow}
    <footer
      in:fly={{ y: 100, duration: 1200, delay: 900 }}
      out:fly={{ y: 100, duration: 300 }}
      class="flex flex-col justify-center items-center flex-none w-full mx-auto xl:mx-0 {className ?? ''}">
      <main
        class="my4 gap1 md:(border-t-2 w-3xl mt-8 border-black border-opacity-20 dark:(border-white border-opacity-10)) flex flex-col justify-center items-center">
        <div class="hidden">
          {Base64.atob(
            'VGhpcyBzaXRlIGlzIHBvd2VyZWQgYnkgUVdFUjogU2ltcGx5IEF3ZXNvbWUgQmxvZyBTdGFydGVyIGJ1aWx0IHdpdGggU3ZlbHRlS2l0IGFuZCBMb3ZlLiBAIGh0dHBzOi8vZ2l0aHViLmNvbS9rd2NoYW5nMDgzMS9zdmVsdGUtUVdFUg==',
          ).toString()}
        </div>
        <div class="flex justify-center mt-4">
          <a use:tippy href="/atom.xml" class="btn btn-ghost" aria-label="Atom Feed">
            <div class="!w-[1.75rem] !h-[1.75rem] i-ic-baseline-rss-feed" />
          </a>
          <a use:tippy href="/sitemap.xml" class="btn btn-ghost" aria-label="Sitemap">
            <div class="!w-[1.75rem] !h-[1.75rem] i-mdi-sitemap-outline" />
          </a>
        </div>
        <p itemprop="copyrightNotice">
          Copyright © <span itemprop="copyrightYear">{copyrightYear}</span>
          <a href={siteConfig.author.github} rel="noopener external" class="hover:(text-sky-500)">
            <span itemprop="copyrightHolder">{siteConfig.author.name}</span>
          </a>
        </p>
      </main>
    </footer>
  {/key}
{/if}
