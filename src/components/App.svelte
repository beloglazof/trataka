<script>
  import { onDestroy } from 'svelte';

  import { tratakaState } from '../stores';
  import Praise from './Praise.svelte';
  import Settings from './Settings.svelte';
  import Actions from './Actions.svelte';

  const HARE = 'Харе';
  const KRISHNA = 'Кришна';
  const RAMA = 'Рама';

  const mahaMantra = [
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
  const LETTERS_IN_MANTRA = mahaMantra.reduce((acc, word) => acc + word.length, 0);

  let letterCounter = 0;
  let wordCounter = 0;
  let mantraCounter = 0;
  let roundCounter = 0;

  let rounds = 1;
  let minutesForRound = 10;

  function handleCounters() {
    const wordEnd = letterCounter === mahaMantra[wordCounter].length;
    if (wordEnd) {
      letterCounter = 0;
      wordCounter += 1;
    }
    const mantraEnd = wordCounter === WORDS_IN_MANTRA;
    if (mantraEnd) {
      hideMantra();
      wordCounter = 0;
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

  $: handleCounters(letterCounter, wordCounter, mantraCounter, roundCounter);

  function hideWord(ind) {
    const wordElement = document.querySelector(`#hn-${ind}`);
    wordElement.classList.add('hidden');
  }

  function showWord(ind) {
    const wordElement = document.querySelector(`#hn-${ind}`);
    wordElement.classList.remove('hidden');
  }

  function hideMantra() {
    mahaMantra.forEach((_, ind) => hideWord(ind));
  }

  function showMantra() {
    mahaMantra.forEach((_, ind) => showWord(ind));
  }

  let inactive = false;
  // let started = false;
  let finished = false;
  let paused = false;

  const unsubscribe = tratakaState.subscribe((state) => {
    switch (state) {
      case tratakaState.inactive:
        inactive = true;
        // started = false;
        finished = false;
        paused = false;
        break;
      case tratakaState.started:
        // started = true;
        inactive = false;
        finished = false;
        paused = false;
        break;
      case tratakaState.finished:
        finished = true;
        inactive = false;
        // started = false;
        paused = false;
        break;
      case tratakaState.paused:
        paused = true;
        // started = false;
        finished = false;
        inactive = false;
        break;
      default:
        break;
    }
  });

  function handleLetterTick() {
    if (letterCounter === 0) {
      showWord(wordCounter);
    }
    letterCounter += 1;
  }

  let mainIntervalId;
  function start() {
    if (inactive) {
      hideMantra();
    }

    tratakaState.start();

    const secondsForRound = minutesForRound * SECONDS_IN_MINUTE;
    const secondsForMantra = secondsForRound / MANTRAS_IN_ROUND;
    // const secondsForWord = secondsForMantra / WORDS_IN_MANTRA;
    const secondForLetter = secondsForMantra / LETTERS_IN_MANTRA;
    // const msForWord = secondsForWord * 1000;
    const msForLetter = secondForLetter * 1000;

    mainIntervalId = setInterval(handleLetterTick, msForLetter);
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
      showMantra();
    }
    tratakaState.reset();
    clearInterval(mainIntervalId);
    mainIntervalId = null;

    letterCounter = 0;
    wordCounter = 0;
    mantraCounter = 0;
    roundCounter = 0;
  }

  onDestroy(() => {
    reset();
    unsubscribe();
  });

  const rowLength = 2;
  function isRowEnd(wordInd) {
    const wordNum = wordInd + 1;
    return wordNum % rowLength === 0;
  }

  const roundsLabel = 'Круги';
  const mantrasLabel = 'Мантры';
  // let wordsLabel = "Слова";
</script>

<main class="main">
  {#if !inactive}
    <div class="counters">
      <div class="counter">
        {roundsLabel}:
        <span class="counter-number">{roundCounter}</span>
      </div>
      <div class="counter">
        {mantrasLabel}:
        <span class="counter-number">{mantraCounter}</span>
      </div>
      <!-- <div class="counter">{wordsLabel}: <span class="counter-number">{wordCounter}</span></div> -->
    </div>
  {/if}

  <section class="mantra-box">
    {#if finished}
      <Praise />
    {:else}
      <div class="mantra-card">
        {#each mahaMantra as word, i (i)}
          <span id={`hn-${i}`} class="holy-name">{word}</span>
          {#if isRowEnd(i)}
            <br />
          {/if}
        {/each}
      </div>
    {/if}
  </section>

  {#if paused || inactive}
    <Settings bind:rounds bind:minutesForRound />
  {/if}

  <Actions {start} {reset} {pause} />
</main>

<style>
  .main {
    padding: 8px 0;
    height: 540px;
    max-width: 400px;
    margin: auto;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .counters {
    display: flex;
    justify-content: flex-end;
    margin-right: 8px;
  }

  .counter {
    font-family: 'Courier New', Courier, monospace;
    font-size: 14px;
    margin-right: 10px;
    color: var(--secondary-text);
  }

  .counter:last-child {
    margin-right: 0;
  }

  .mantra-box {
    display: flex;
    justify-content: center;
    text-align: center;
    flex: 1;
    width: 100%;
    height: 100%;
    margin: 40px 0;
  }

  .mantra-card {
    font-size: 36px;
    font-weight: 500;
  }

  .holy-name {
    transition: opacity 0.1s cubic-bezier(0.645, 0.045, 0.355, 1);
  }
</style>
