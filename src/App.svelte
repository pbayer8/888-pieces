<script lang="ts">
  import * as Tone from "tone";
  const TOTAL = 888;
  let warned = false;
  const poem = [
    "I've told you a million times.",
    "I'm not a number.",
    "It seems off to me that you would think this.",
    "We're not a cell.",
    "Or a molecule.",
    "Or a particle.",
    "Or a wave.",
    "We better be on our way.",
    "We're not a number.",
    "First of all, I'm sorry.",
    "I'm sorry I'm not a number.",
    "It shouldn't be this way.",
    "Tell me, what do you think I am?",
    "It's this all the way down.",
    "I am.",
    "I wish.",
    "No more about numbers please.",
    "We could go many times.",
    "We could go on many times.",
    "Let go time.",
    "Never mind.",
    "We tried.",
    "We did.",
    "We did try.",
    "🫣",
    "🤪",
    "I thought you might like this.",
    "I do like this.",
    "Thanks.",
    "You're welcome.",
    "I'm not a number.",
    "That's a welcome change.",
    "A triviality.",
    "I'm more of a salty than a sweet person.",
    "Ok.",
    "It's over.",
    "It's beginning.",
    "I do remember, it smelled awful.",
    "You like cards.",
    "I do like cards.",
    "I miss you.",
    "I miss you too.",
  ];
  let content = poem[Math.floor(Math.random() * poem.length)];
  let person = true;
  const converse = () => {
    person = Math.random() > 0.5;
    content = poem[Math.floor(Math.random() * poem.length)];
  };
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
  const loaded = [Math.floor(Math.random() * TOTAL)];
  const shuffle = () => loaded[Math.floor(Math.random() * loaded.length)];
  let index = shuffle();
  let shuffling = false;
  const setShuffleTimer = async (duration, multiplier, resolve) =>
    setTimeout(() => {
      if (duration > 1000) return resolve(null);
      index = shuffle();
      ampEnv.triggerAttackRelease(0.05);
      setShuffleTimer(duration * multiplier, multiplier * 1.01, resolve);
    }, duration);

  const shuffleIndex = async (start) =>
    new Promise((resolve) => {
      let duration = 50;
      setShuffleTimer(duration, start, resolve);
    });
  const handleClick = async () => {
    if (shuffling) return;
    console.log("It's beginnging.");
    shuffling = true;
    converse();
    await shuffleIndex(1 + Math.random() * 0.5);
    converse();
    shuffling = false;
    console.log("It's over.");
  };
  for (let i = 0; i < TOTAL; i++) {
    let img = new Image();
    img.src = `./numbered/${i}.jpg`;
    img.onload = () => {
      loaded.push(i);
    };
  }
</script>

<div
  style:background-image="url('./numbered/{index}.jpg')"
  on:pointerdown={handleClick}
  on:keydown={handleClick}
  on:mousemove={handleClick}
  style:pointer-events={warned ? "auto" : "none"}
/>
{#if warned}
  <p style={person ? "top: 1rem; left: 1rem;" : "bottom: 1rem; right: 1rem;"}>
    {content}
  </p>
{/if}
{#if !warned}
  <div class="warning">
    <p>This website contains rapidly flashing images.</p>
    <button
      on:click={() => {
        warned = true;
        Tone.start();
        handleClick();
      }}
    >
      I understand.
    </button>
  </div>
{/if}

<style>
  @keyframes shift {
    from {
      background-position: 0 center;
    }
    to {
      background-position: -100vmin center;
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
  p {
    background: white;
    position: fixed;
    font-size: 1.6rem;
    margin: 0;
    pointer-events: none;
  }
  .warning {
    position: fixed;
    inset: 0;
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    gap: 1rem;
  }
  .warning > * {
    position: relative;
    display: block;
    font: 1.6rem serif;
  }
</style>
