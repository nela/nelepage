<script lang="ts">
  import Cube from "./lib/Cube.svelte";

  const logoMetadata: Record<
    string,
    { alt: string; path: string; background: string }
  > = {
    github: {
      alt: "github-icon",
      path: "../public/github-mark.svg",
      background: "linear-gradient(#4f4f4f, #333);",
    },
    linked: {
      alt: "linked-in-icon",
      path: "../public/linkedin.svg",
      background: "linear-gradient(#00a0dc, #0077b5);",
    },

    soundcloud: {
      alt: "soundcloud-icon",
      path: "../public/soundcloud.svg",
      background: "linear-gradient(#faa21b, #f15623);",
    },
    uio: {
      alt: "uio-icon",
      path: "../public/uio.svg",
      background: "linear-gradient(#fb6666, #dd0000)",
    },
  };
</script>

<main>
  Nelescope

  <div class="cubes">
    <Cube logoMetadata={logoMetadata['github']} />
    <Cube logoMetadata={logoMetadata['linked']} />
    <Cube logoMetadata={logoMetadata['soundcloud']} />
    <Cube logoMetadata={logoMetadata['uio']} />
  </div>
</main>

<style lang="scss">
  .cubes {
    display: flex;
    align-items: center;
    justify-items: center;
  }
</style>
