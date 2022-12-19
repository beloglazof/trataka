<script>
  import { onDestroy } from 'svelte';

  import { tratakaState } from '../stores';
  import Play from './icons/Play.svelte';
  import Pause from './icons/Pause.svelte';
  import Reset from './icons/Reset.svelte';

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

  onDestroy(unsubscribe);

  export let start = () => {};
  export let reset = () => {};
  export let pause = () => {};

  const startButtonName = 'Старт';
  const resetButtonName = 'Сброс';
  const pauseButtonName = 'Пауза';
</script>

<div class="actions">
  {#if paused || finished}
    <button class="action-button" type="button" on:click={reset} title={resetButtonName}>
      <span class="icon-wrapper">
        <Reset />
      </span>
    </button>
  {/if}
  {#if started}
    <button class="action-button" type="button" on:click={pause} title={pauseButtonName}>
      <span class="icon-wrapper">
        <Pause />
      </span>
    </button>
  {/if}
  {#if inactive || paused}
    <button class="action-button" type="button" on:click={start} title={startButtonName}>
      <span class="icon-wrapper">
        <Play />
      </span>
    </button>
  {/if}
</div>

<style>
  .actions {
    width: 100%;
    display: flex;
    justify-content: center;
  }

  .action-button {
    padding: 0;
    border: none;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: inherit;
  }

  .action-button:last-child {
    margin-right: 0;
  }
</style>
