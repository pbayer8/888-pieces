<script lang="ts">
  let index = 0;
  let shuffling = false;
  const setShuffleTimer = async (duration, multiplier, resolve) =>
    setTimeout(() => {
      if (duration > 1000) return resolve(null);
      index = Math.floor(Math.random() * 888);
      setShuffleTimer(duration * multiplier, multiplier * 1.01, resolve);
    }, duration);

  const shuffleIndex = async () =>
    new Promise((resolve) => {
      let duration = 50;
      setShuffleTimer(duration, 1, resolve);
    });
  const handleClick = async () => {
    if (shuffling) return;
    console.log("It's beginnging.");
    shuffling = true;
    await shuffleIndex();
    shuffling = false;
    console.log("It's over.");
  };
</script>

<div
  style:background-image="url('/numbered/{index}.png')"
  on:click={handleClick}
  on:keydown={handleClick}
  on:mousemove={handleClick}
/>

<style>
  @keyframes shift {
    from {
      background-position: 0 0;
    }
    to {
      background-position: -100vmin 0;
    }
  }
  @keyframes scale {
    from {
      background-size: 90vmin;
    }
    to {
      background-size: 100vmin;
    }
  }
  div {
    background-position: center;
    background-size: 100vmin;
    width: 100%;
    height: 100%;
    animation: shift 10s linear infinite;
  }
</style>
