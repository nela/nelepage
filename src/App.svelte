<script lang="ts">
  import Cube from "./lib/Cube.svelte";
  import ghLogo from "./assets/github.svg";
  import uioLogo from "./assets/uio.svg";
  import scLogo from "./assets/soundcloud.svg";
  import liLogo from "./assets/linkedin.svg";
  import Header from "./lib/Header.svelte";
  import { fade, fly } from "svelte/transition";
  import { elasticOut, sineIn } from "svelte/easing";
  import { onMount } from "svelte";

  let visible = false;

  const logoMetadata: Record<
    string,
    { alt: string; path: string; background: string; url: string }
  > = {
    github: {
      alt: "github-icon",
      path: ghLogo,
      background: "linear-gradient(135deg, #4f4f4f, #333);",
      url: "https://github.com/nela/",
    },
    linked: {
      alt: "linked-in-icon",
      path: liLogo,
      background: "linear-gradient(135deg, #00a0dc, #0077b5);",
      url: "",
    },
    soundcloud: {
      alt: "soundcloud-icon",
      path: scLogo,
      background: "linear-gradient(135deg, #faa21b, #f15623);",
      url: "",
    },
    uio: {
      alt: "uio-icon",
      path: uioLogo,
      background: "linear-gradient(135deg, #fb6666, #dd0000)",
      url: "",
    },
  };

  onMount(() => {
    visible = true;
  });
</script>

<main>
  {#if visible}
  <div id="header"
      class="header"
      in:fade={{ duration: 1000, delay: 500, easing: sineIn}}
    >
    <Header />
  </div>

  <div  class='cubes' id="cubes"
    in:fly={{ y: "100vh", duration: 2000, delay: 200, opacity: 0.3, easing: elasticOut}}>
    <Cube logoMetadata={logoMetadata['github']} />
    <Cube logoMetadata={logoMetadata['linked']} />
    <Cube logoMetadata={logoMetadata['soundcloud']} />
    <Cube logoMetadata={logoMetadata['uio']} />
  </div>
{/if}

</main>

<style lang="scss">
  .cubes {
    display: flex;
    align-items: center;
    justify-items: center;
    transform: translateY(calc(var(--b) * 10px));
  }
</style>
