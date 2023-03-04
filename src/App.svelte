<script lang="ts">
  import * as Tone from "tone";
  const TOTAL = 888;
  // const input = new Tone.Oscillator(230, "sawtooth").start();
  // input.connect(shift);
  const ampEnv = new Tone.AmplitudeEnvelope({
    attack: 0.01,
    decay: 0.01,
    sustain: 0.1,
    release: 0.001,
  }).toDestination();
  const osc = new Tone.OmniOscillator({ type: "triangle" })
    .connect(ampEnv)
    .start();
  var lfo = new Tone.LFO(0.01, 0.01, 0.1); // hertz, min, max
  var lfo2 = new Tone.LFO(0.1, 70, 280); // hertz, min, max
  lfo.connect(lfo2.frequency);
  lfo2.connect(osc.frequency);
  lfo.start();
  lfo2.start();
  ampEnv.triggerAttackRelease("8t");
  const shuffle = () => Math.floor(Math.random() * TOTAL);
  let index = shuffle();
  let shuffling = false;
  const setShuffleTimer = async (duration, multiplier, resolve) =>
    setTimeout(() => {
      if (duration > 1000) return resolve(null);
      index = shuffle();
      ampEnv.triggerAttackRelease(0.05);
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

<svelte:body on:click={() => Tone.start()} />

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
