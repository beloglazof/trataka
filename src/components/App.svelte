<script>
  import { onDestroy } from 'svelte';

  import { tratakaState } from '../stores';
  import Praise from './Praise.svelte';
  import Settings from './Settings.svelte';
  import Actions from './Actions.svelte';
  import Row from './Row.svelte';

  const HARE = 'Харе';
  const KRISHNA = 'Кришна';
  const RAMA = 'Рама';
  const MAHA_MANTRA = [
    HARE,
    KRISHNA,
    HARE,
    KRISHNA,
    KRISHNA,
    KRISHNA,
    HARE,
    HARE,
    HARE,
    RAMA,
    HARE,
    RAMA,
    RAMA,
    RAMA,
    HARE,
    HARE
  ];
  const MANTRAS_IN_ROUND = 108;
  const SECONDS_IN_MINUTE = 60;
  const WORDS_IN_MANTRA = 16;
  const ROW_LENGTH = 2;

  let wordCounter = 0;
  let rowCounter = 0;
  let mantraCounter = 0;
  let roundCounter = 0;

  let wordsInView = [KRISHNA, KRISHNA];
  let rounds = 1;
  let minutesForRound = 20;

  $: secondsForRound = minutesForRound * SECONDS_IN_MINUTE;
  $: secondsForMantra = secondsForRound / MANTRAS_IN_ROUND;
  $: secondsForWord = secondsForMantra / WORDS_IN_MANTRA;
  $: msForWord = secondsForWord * 1000;
  $: tickDelay = msForWord * ROW_LENGTH;

  function handleCounters() {
    wordCounter = ROW_LENGTH * rowCounter;

    const mantraEnd = wordCounter === WORDS_IN_MANTRA;
    if (mantraEnd) {
      wordCounter = 0;
      rowCounter = 0;
      mantraCounter += 1;
    }

    const roundEnd = mantraCounter === MANTRAS_IN_ROUND;
    if (roundEnd) {
      mantraCounter = 0;
      roundCounter += 1;
    }

    if (roundCounter && roundCounter === rounds) {
      finish();
    }
  }

  $: handleCounters(wordCounter, rowCounter, mantraCounter, roundCounter);

  let inactive = false;
  let started = false;
  let finished = false;
  let paused = false;

  const unsubscribe = tratakaState.subscribe((state) => {
    switch (state) {
      case tratakaState.inactive:
        inactive = true;
        started = false;
        finished = false;
        paused = false;
        break;
      case tratakaState.started:
        started = true;
        inactive = false;
        finished = false;
        paused = false;
        break;
      case tratakaState.finished:
        finished = true;
        inactive = false;
        started = false;
        paused = false;
        break;
      case tratakaState.paused:
        paused = true;
        started = false;
        finished = false;
        inactive = false;
        break;
      default:
        break;
    }
  });

  function handleTick() {
    const startIndex = ROW_LENGTH * rowCounter;
    wordsInView = MAHA_MANTRA.slice(startIndex, startIndex + ROW_LENGTH);

    rowCounter += 1;
  }

  let mainIntervalId;
  function start() {
    if (inactive) {
      wordsInView = [];
    }

    tratakaState.start();
    handleTick();
    mainIntervalId = setInterval(handleTick, tickDelay);
  }

  function finish() {
    tratakaState.finish();
    clearInterval(mainIntervalId);
    mainIntervalId = null;
  }

  function pause() {
    tratakaState.pause();
    clearInterval(mainIntervalId);
    mainIntervalId = null;
  }

  function reset() {
    if (paused) {
      wordsInView = [HARE, KRISHNA];
    }

    tratakaState.reset();
    clearInterval(mainIntervalId);
    mainIntervalId = null;

    wordCounter = 0;
    mantraCounter = 0;
    roundCounter = 0;
    rowCounter = 0;
  }

  onDestroy(() => {
    reset();
    unsubscribe();
  });
</script>

<main class="main">
  {#if !finished}
    <section class="mantra-box">
      <div class="mantra-card">
        {#key rowCounter}
          <Row words={wordsInView} wordDelay={msForWord} />
        {/key}
      </div>
    </section>

    <div class="image-wrapper">
      <div>
        <img
          class="image"
          src="images/panca-tattva.jpg"
          alt="Pancha-Tattva: Advaita-Nityananda-Chaitanya-Gadadhara-Srivasa"
          title="Advaita-Nityananda-Chaitanya-Gadadhara-Srivasa"
          width="783"
          height="1044"
        />
      </div>
    </div>
  {:else}
    <Praise />
  {/if}

  <Actions {start} {reset} {pause} />

  {#if paused || inactive}
    <Settings bind:rounds bind:minutesForRound />
  {/if}
  <!-- <div>
    <p>
      Круги: {roundCounter}
      Мантры: {mantraCounter}
    </p>
  </div> -->
</main>

<style>
  .main {
    padding: 8px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .image-wrapper {
    max-width: 600px;
  }

  .image {
    display: block;
    width: 100%;
    height: 100%;
    border-radius: 4px;
  }

  .mantra-box {
    margin: 16px 0 24px 0;
    padding: 4px 8px;
    display: flex;
    justify-content: center;
    border: 1px solid #333;
    border-radius: 4px;
    text-align: center;
  }
</style>
