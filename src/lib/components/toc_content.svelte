<script lang="ts">
  import { slide } from 'svelte/transition';
  import type { TOC } from '$lib/types/toc';
  import { tocCur } from '$stores/toc';

  export let content: TOC.Heading;
  export let expanded = false;
  export let depth = 1;
</script>

<li id={content.slug}>
  <div
    on:click={() => {
      const heading = document.getElementById(`${content.slug.substring(1)}`);
      const header_nav = document.getElementById('header-nav');
      if (heading && header_nav) {
        const top = heading.offsetTop - header_nav.clientHeight;
        window.scrollTo({ top, behavior: 'smooth' });
      }
    }}
    toc-link
    class="z1 group flex items-center gap2 py2 {$tocCur.get(content.slug) ? 'border-[#0096FF]' : 'border-transparent'}"
    class:pl4={depth === 1}
    class:pl8={depth === 2}
    class:pl12={depth === 3}
    class:pl16={depth === 4}
    class:pl18={depth === 5}>
    {#if content.child && content.child.length > 0}
      <span
        on:click|stopPropagation={() => {
          expanded = !expanded;
        }}
        class="cursor-pointer z10 !w-[1.25rem] !h-[1.25rem] inline-block transition-transform duration-300 ease-out shrink-0 {expanded
          ? 'i-akar-icons-circle-chevron-up active:translate-y--1 hover:i-akar-icons-circle-chevron-up-fill'
          : 'i-akar-icons-circle-chevron-down active:translate-y-1 hover:i-akar-icons-circle-chevron-down-fill'}" />
    {:else}
      <span class="!w-[1.25rem] !h-[1.25rem]  i-akar-icons-circle inline-block shrink-0" />
    {/if}
    <span
      class="cursor-pointer select-none text-black/[0.6] group-hover:(text-black) dark:(text-white/[0.6] group-hover:(text-white) group-active:(!text-black))">
      {@html content.heading}
    </span>
  </div>
  {#if content.child && content.child.length > 0}
    {#if expanded}
      <ul transition:slide={{ duration: 300 }} class="flex flex-col">
        {#each content.child as c}
          <svelte:self content={c} depth={depth + 1} expanded />
        {/each}
      </ul>
    {/if}
  {/if}
</li>

<style>
  [toc-link] {
    --at-apply: 'border-l-4 hover:bg-gray/[0.25] active:(bg-yellow) dark:(active:(text-black/[0.6]))';
  }
</style>
