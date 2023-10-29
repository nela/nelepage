<script lang="ts">
  import { spring } from "svelte/motion";

  export let logoMetadata: { alt: string, path: string, background: string };
  export let deg = 40;
  export let rotation = 0;

  let fx = 0, fy = 0;
  let cubePositions = ['', 'left', 'right', 'top', 'bottom', 'back']

  const boop = (node: HTMLElement) => {
    let booping = spring(0, { stiffness: 0.05, damping: 0.05 });
    let timeout: number | undefined;
    const unsubscribe = booping.subscribe((v) => rotation = v);

    const onEnter = (e: MouseEvent) => {
      const { x, y, width, height } = node.getBoundingClientRect();

      fy = x + width / 2 < e.x ? -1 : 1;
      fx = y + height / 2 < e.y ? -1 : 1;

      booping.set(deg);
      clearTimeout(timeout);
      timeout = setTimeout(() => { booping.set(0) }, 150);
    };

    node.addEventListener("mouseenter", onEnter);

    return {
      destroy: () => {
        unsubscribe();
        node.removeEventListener("mouseenter", onEnter);
      }
    };
  };
</script>

<div class="root">
  <div class="cube"
    use:boop
    style={`--fx:${fx};--fy:${fy};--deg:${rotation};--bg:${logoMetadata.background}`}
  >
    {#each cubePositions as pos}
      {#if pos == 'back' }
        <div class="area {pos}" />
      {:else}
        <div class="area {pos}" >
          <div class="icon-container">
            {#each Array(5) as _, index (index)}
              <img alt={logoMetadata.alt} src={logoMetadata.path}/>
            {/each}
          </div>
        </div>
      {/if}
    {/each}
  </div>
</div>

<style lang="scss">
  :root {
    --fx: 0;
    --fy: 0;
    --deg: 0;
    --bg: '':
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
    transform:
      rotate3d(var(--fx), var(--fy), 0, calc(var(--deg) * 1deg))
      skewX(calc(var(--fy) * var(--deg)/10 * 1deg))
      skewY(calc(var(--fx) * var(--deg)/10 * 1deg));
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

    &.right {
      transform: translateZ(-$cube-area-half)
        translate($cube-area-half, 0)
        rotateY(90deg);
    }

    &.left {
      transform: translateZ(-$cube-area-half)
        translate(-$cube-area-half, 0)
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

    transform-origin: center;
    transform: skewX(calc(var(--fy) * var(--deg)/5 * 1deg))
      skewY(calc(var(--fx) * var(--deg)/5 * 1deg));
    transform-style: preserve-3d;

    & img {
      position: absolute;
      width: 67%;
      height: 67%;
    }

    @for $i from 2 through 5 {
      & img:nth-child(#{$i}) {
        transform: translate(calc(var(--deg) * $i * 0.1px));
        opacity: calc(1 - ($i/10));
      }
    }
  }


</style>
