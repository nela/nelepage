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
  export let degOnOut = 1.4;
  export let degOnMove = 0.6;
  export let rotation = 0;
  let hw: number, hh: number;
  let rx: number, ry: number;

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
    let centre: { x: number; y: number };

    let booping = spring(0, { stiffness: 0.05, damping: 0.05 });
    let unsubscribe = booping.subscribe((v) => (rotation = v));

    const onMove = (e: MouseEvent) => {
      booping.set(degOnMove);
      ry = -(centre.x - e.x) / hw
      rx = (centre.y - e.y) / hh
    };

    const onLeave = (e: MouseEvent) => {
      node.removeEventListener("mousemove", onMove);
      ry = -1 * (centre.x - e.x) / hw
      rx = (centre.y - e.y) / hh
      booping.set(degOnOut);
      clearTimeout(timeout);
      timeout = setTimeout(() => { booping.set(0); }, 150);
    };

    const onEnter = (_: MouseEvent) => {
      const { x, y, width, height } = node.getBoundingClientRect();
      hw = width/2
      hh = height/2
      centre = { x: x + hw, y: y + hh};
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
      style={`--rx:${rx};--ry:${ry};--rot:${rotation};--bg:${logoMetadata.background};`}
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
      <!-- <div class="area front">
        <div class="icon-container">
          {#each Array(5) as _, index (index)}
            <img alt={logoMetadata.alt} src={logoMetadata.path} />
          {/each}
        </div>
      </div> -->
    </div>
  </div>
{/if}

<style lang="scss">
  :root {
    --rot: 0;
    --rx: 0;
    --ry: 0;
  }

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
    transform: rotateX(calc(var(--rx) * var(--rot) * 1rad))
      rotateY(calc(var(--rot) * 1rad * var(--ry)))
      skewY(calc(var(--rot) * 0.1rad))
      skewX(calc(var(--rot) * 0.1rad));
    transform-style: preserve-3d;
    transform-origin: center center;
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
    transform-origin: center center;;

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
    transform-origin: center center;
    // transform: skewX(calc(var(--fy) * var(--deg) / 5 * 1deg))
    //   skewY(calc(var(--fx) * var(--deg) / 5 * 1deg));
    transform-style: preserve-3d;
    transform: rotateX(calc(var(--rx) * var(--rot) * 1rad))
      rotateY(calc(var(--rot)* 1rad * var(--ry)))
      skewY(calc(var(--rot) * 0.1rad))
      skewX(calc(var(--rot) * 0.1rad));

    & img {
      transition: transform 500ms cubic-bezier(0.175, 0.885, 0.32, 1.275);
      position: absolute;
      width: 67%;
      height: 67%;
    }

    @for $i from 2 through 5 {
      & img:nth-child(#{$i}) {
        transform: translate(calc(var(--rot) * $i * 2.5px));
        opacity: calc(1 - ($i/10));
      }
    }
  }
</style>
