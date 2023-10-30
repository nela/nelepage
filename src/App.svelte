<script lang="ts">
  import Cube from "./lib/Cube.svelte";
  import ghLogo from "./assets/github.svg";
  import uioLogo from "./assets/uio.svg";
  import scLogo from "./assets/soundcloud.svg";
  import liLogo from "./assets/linkedin.svg";

  const logoMetadata: Record<
    string,
    { alt: string; path: string; background: string; url: string }
  > = {
    github: {
      alt: "github-icon",
      path: ghLogo,
      background: "linear-gradient(#4f4f4f, #333);",
      url: "https://github.com/nela/",
    },
    linked: {
      alt: "linked-in-icon",
      path: liLogo,
      background: "linear-gradient(#00a0dc, #0077b5);",
      url: "",
    },
    soundcloud: {
      alt: "soundcloud-icon",
      path: scLogo,
      background: "linear-gradient(#faa21b, #f15623);",
      url: "",
    },
    uio: {
      alt: "uio-icon",
      path: uioLogo,
      background: "linear-gradient(#fb6666, #dd0000)",
      url: "",
    },
  };
</script>

<main>
  Nelescope

  <div class='cubes'>
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
