<script lang="ts">
  import { onMount } from "svelte";
  import { elasticOut } from "svelte/easing";
  import { spring } from "svelte/motion";
  import { fly } from "svelte/transition";

  export let logoMetadata: {
    alt: string;
    path: string;
    background: string;
    url: string;
  };
  export let degOnOut = 50;
  export let degOnMove = 25;
  export let rotation = 0;

  let fx = 0, fy = 0;
  let cubePositions = ["front", "left", "right", "top", "bottom", "back"];
  let visible = false;
  let startX: number, startY: number, startDuration: number, startDelay: number;

  onMount(() => {
    startX = Math.random() * 1000 - 500;
    startY = Math.random() * 1000 - 500;
    startDuration = Math.random() * 1000 + 1000;
    startDelay = 0;
    visible = true;
  });

  const boop = (node: HTMLElement) => {
    let timeout: number | undefined;
    let center: { x: number; y: number };

    let booping = spring(0, { stiffness: 0.05, damping: 0.05 });
    let unsubscribe = booping.subscribe((v) => (rotation = v));

    const onMove = (e: MouseEvent) => {
      booping.set(degOnMove);
      fy = center.x < e.x ? 1 : -1;
      fx = center.y < e.y ? -1 : 1;
    };

    const onLeave = (e: MouseEvent) => {
      node.removeEventListener("mousemove", onMove);
      fy = center.x < e.x ? 1 : -1;
      fx = center.y < e.y ? -1 : 1;
      booping.set(degOnOut);
      clearTimeout(timeout);
      timeout = setTimeout(() => { booping.set(0); }, 150);
    };

    const onEnter = (_: MouseEvent) => {
      const { x, y, width, height } = node.getBoundingClientRect();
      center = { x: x + width / 2, y: y + height / 2 };
      node.addEventListener("mousemove", onMove);
    };

    node.addEventListener("mouseleave", onLeave);
    node.addEventListener("mouseenter", onEnter);

    return {
      destroy: () => {
        unsubscribe();
        node.removeEventListener("mouseleave", onLeave);
        node.removeEventListener("mouseenter", onEnter);
      },
    };
  };
</script>

{#if visible}
  <div
    in:fly={{
      y: startY,
      x: startX,
      duration: startDuration,
      delay: startDelay,
      opacity: 0.3,
      easing: elasticOut,
    }}
    class="root"
  >
    <div
      class="cube"
      use:boop
      style={`--fx:${fx};--fy:${fy};--deg:${rotation};--bg:${logoMetadata.background}`}
    >
      {#each cubePositions as pos}
        {#if pos == "back"}
          <div class="area {pos}" />
        {:else if pos == "front"}
          <a class="area front" href={logoMetadata.url}>
            <div class="icon-container">
              {#each Array(5) as _, index (index)}
                <img alt={logoMetadata.alt} src={logoMetadata.path} />
              {/each}
            </div>
          </a>
        {:else}
          <div class="area {pos}">
            <div class="icon-container">
              {#each Array(5) as _, index (index)}
                <img alt={logoMetadata.alt} src={logoMetadata.path} />
              {/each}
            </div>
          </div>
        {/if}
      {/each}
    </div>
  </div>
{/if}

<style lang="scss">
  .root {
    position: relative;
    padding: 10px;
    margin: 1rem;
    width: $root-width;
    height: $root-height;
  }

  .cube {
    height: 100%;
    width: 100%;
    transition: transform 500ms cubic-bezier(0.175, 0.885, 0.32, 1.275);
    transform: rotate3d(var(--fx), var(--fy), 0, calc(var(--deg) * 1deg))
      skewX(calc(var(--fy) * var(--deg) / 10 * 1deg))
      skewY(calc(var(--fx) * var(--deg) / 10 * 1deg));
    transform-style: preserve-3d;
    transform-origin: center;
  }

  .area {
    position: absolute;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 8px;
    background: var(--bg);

    &.front {
      z-index: 0;
    }

    &.right {
      transform: translateZ(-$cube-area-half) translate($cube-area-half, 0)
        rotateY(90deg);
    }

    &.left {
      transform: translateZ(-$cube-area-half) translate(-$cube-area-half, 0)
        rotateY(-90deg);
    }

    &.bottom {
      transform: rotateX(-90deg) translateZ($cube-area-half)
        translate(0, $cube-area-half);
    }

    &.top {
      transform: rotateX(90deg) translateZ($cube-area-half)
        translate(0, -$cube-area-half);
    }

    &.back {
      transform: translateZ(-$cube-area);
    }
  }

  .icon-container {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
    position: relative;

    transition: transform 500ms cubic-bezier(0.175, 0.885, 0.32, 1.275);
    transform-origin: center;
    transform: skewX(calc(var(--fy) * var(--deg) / 5 * 1deg))
      skewY(calc(var(--fx) * var(--deg) / 5 * 1deg));
    transform-style: preserve-3d;

    & img {
      position: absolute;
      width: 67%;
      height: 67%;
    }

    @for $i from 2 through 5 {
      & img:nth-child(#{$i}) {
        transition: transform 500ms cubic-bezier(0.175, 0.885, 0.32, 1.275);
        transform: translate(calc(var(--deg) * $i * 0.1px));
        opacity: calc(1 - ($i/10));
      }
    }
  }
</style>
