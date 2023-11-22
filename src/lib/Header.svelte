<script>
  const first = "Nemanja";
  const last = "Lakicevic";
</script>

<div class="name-wrapper diagonal-gradient">
  <div class="first-name ">
    {#each first as letter, i}
      <span style="--i:{i};">{letter}</span>
    {/each}
  </div>
  <div class="last-name">
    {#each last as letter, i}
      <span style="--i:{i + first.length};">{letter}</span>
    {/each}
  </div>
</div>

<style lang="scss">
  .name-wrapper {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    position: relative;
    font-family: 'Josefin Sans Variable';
    font-weight: 600;
    font-size: 100px;

    .first-name, .last-name {
      height: 100%;
      width: 100%;
    }

    span {
      position: relative;
      display: inline-block;
      animation: flip 20s infinite;
      animation-delay: calc(.2s * var(--i));
    }
  }

  @keyframes flip {
    0%, 80% {
      transform: rotateY(360deg);
    }
  }

  .diagonal-gradient {
    background: linear-gradient(135deg, #ededed, #bdbdbd, #e1dada, #f1f1f1, #F5E3E6, #D9E4F5 );
    background-clip: text;
    -webkit-background-clip: text;
    color: transparent;
    -webkit-text-fill-color: transparent;
    background-size: 500% 500%;
    animation: diagonalFlow 10s ease-in-out infinite alternate;
    text-shadow:
      3px 7px 8px rgba(241, 220, 223, 0.25),
      0px -4px 10px rgba(222, 228, 238, 0.25);
  }

  @keyframes diagonalGradient {
    0% {
      background-position: 0% 50%;
    }

    100% {
      background-position: 100% 50%;
    }
  }
</style>
