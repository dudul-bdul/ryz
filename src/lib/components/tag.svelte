<script lang="ts">
  import type { Tags } from '$lib/types/tags';
  export let data: Tags.Tag;

  import { tagsCur } from '$stores/tags';
  import { postsShow } from '$stores/posts';
  import { browser } from '$app/environment';
  import { page } from '$app/stores';

  function handleClick() {
    tagsCur.toggle(data);
    postsShow.filter($tagsCur);
    if (browser && window.location.pathname === '/') {
      let output = new URLSearchParams();
      $page.url.searchParams.forEach((v, k) => {
        if (k.match(/^(?!(tags(-.*)?))/)) output.append(k, v);
      });
      $tagsCur.forEach((v, k) => {
        output.append(k === 'tags' ? k : `tags-${encodeURI(k)}`, Array.from(v).join(','));
      });
      const params = output.toString();
      if (params) window.history.replaceState({}, '', `?${params}`);
      else window.history.replaceState({}, '', '/');
    }
  }
</script>

{#key $tagsCur}
  <button
    class:btn_active={tagsCur.has(data)}
    class="text-sm m-1 normal-case border-2 border-dotted btn hover:(border-[#007300] border-solid) border-black/[0.5] dark:(border-white/[0.5]) active:(scale-80 transition-transform duration-250 ease-in-out)"
    on:click={handleClick}>
    {data.name}
  </button>
{/key}

<style>
  .btn_active {
    --at-apply: 'font-bold shadow-lg bg-black/[0.6] text-white dark:(bg-white/[0.85] text-black) hover:(border-[#CC0000] border-solid)';
  }
</style>
