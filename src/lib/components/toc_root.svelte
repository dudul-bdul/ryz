<script lang="ts">
  import type { TOC } from '$lib/types/toc';
  import TocContent from '$lib/components/toc_content.svelte';
  export let toc: TOC.Heading[] | undefined;
  import { tocCur } from '$stores/toc';
  import { browser } from '$app/environment';

  let pos = { top: 0, left: 0, x: 0, y: 0 };

  function mouseDownHandler(e: { clientX: number; clientY: number }) {
    const post_toc = document.getElementById('post-toc');
    if (post_toc) {
      post_toc.style.cursor = 'grabbing';
      post_toc.style.userSelect = 'none';

      pos = {
        left: post_toc.scrollLeft,
        top: post_toc.scrollTop,
        x: e.clientX,
        y: e.clientY,
      };
    }

    document.addEventListener('mousemove', mouseMoveHandler);
    document.addEventListener('mouseup', mouseUpHandler);
  }

  function mouseMoveHandler(e: { clientX: number; clientY: number }) {
    const post_toc = document.getElementById('post-toc');
    if (post_toc) {
      const dx = e.clientX - pos.x;
      const dy = e.clientY - pos.y;

      post_toc.scrollTop = pos.top + dy;
      post_toc.scrollLeft = pos.left - dx;
    }
  }

  function mouseUpHandler() {
    const post_toc = document.getElementById('post-toc');
    if (post_toc) {
      post_toc.style.cursor = 'grab';
      post_toc.style.removeProperty('user-select');
    }
    document.removeEventListener('mousemove', mouseMoveHandler);
    document.removeEventListener('mouseup', mouseUpHandler);
  }

  let box: Element;
  let boxH: number;
  let upMore: boolean = false;
  let downMore: boolean = false;

  $: if (browser && box) {
    const top = 0;
    const bot = box.scrollHeight - boxH;
    upMore = box.scrollTop > top;
    downMore = box.scrollTop < bot;
  }

  function handleScroll() {
    const top = 0;
    const bot = box.scrollHeight - boxH;
    upMore = box.scrollTop > top;
    downMore = box.scrollTop < bot;
  }

  function handleUpMore() {
    if (upMore) {
      const post_toc = document.getElementById('post-toc');
      if (post_toc) {
        post_toc.scrollBy({ top: -boxH, behavior: 'smooth' });
      }
    }
  }

  function handleDownMore() {
    if (downMore) {
      const post_toc = document.getElementById('post-toc');
      if (post_toc) {
        post_toc.scrollBy({ top: boxH, behavior: 'smooth' });
      }
    }
  }

  let scrollY: number;
  let lastY: number = 0;
  let scrollingUp: boolean = false;

  $: if (browser) {
    scrollingUp = lastY - scrollY > 0;
    lastY = scrollY;

    const post_toc = document.getElementById('post-toc');
    const activated = Array.from($tocCur.keys());
    const lastActivated = activated.length > 0 ? document.getElementById(activated[activated.length - 1]) : undefined;

    if (post_toc && lastActivated) {
      const cTop = post_toc.scrollTop;
      const cBot = cTop + post_toc.clientHeight;
      const aTop = lastActivated.offsetTop - post_toc.offsetTop;
      const aBot = aTop + lastActivated.clientHeight;
      const isInView = aTop >= cTop && aBot <= cBot;
      if (!isInView) {
        const top = scrollingUp ? aTop : aBot;
        post_toc.scrollTo({ top, behavior: 'smooth' });
      }
    }
  }
</script>

<svelte:window bind:scrollY />

{#if toc && toc.length > 0}
  <aside aria-label="Table Of Content" class="sticky top-[4rem] hidden xl:block pb8">
    <nav on:mousedown={mouseDownHandler} class="flex-col">
      <h2 class="text-2xl font-bold px4 py2">Table of Content</h2>
      <div on:click={handleUpMore} class={upMore ? 'cursor-pointer  hover:bg-gray/[0.5]' : ''}>
        <div class="i-bxs-chevrons-up w6 h6 m-auto {upMore ? 'op100' : 'op0'}" />
      </div>
      {#if toc && toc.length > 0}
        <ul
          bind:this={box}
          bind:clientHeight={boxH}
          on:scroll={handleScroll}
          id="post-toc"
          class="my2 text-base font-semibold flex flex-col max-h-60vh cursor-grab overflow-hidden">
          {#each toc as c}
            <TocContent content={c} expanded />
          {/each}
        </ul>
      {/if}
      <div on:click={handleDownMore} class={downMore ? 'cursor-pointer hover:bg-gray/[0.5]' : ''}>
        <div class="i-bxs-chevrons-down w6 h6 m-auto {downMore ? 'op100 ' : 'op0'}" />
      </div>
    </nav>
  </aside>
{/if}
