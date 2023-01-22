<script>
  import Timer from './icons/Timer.svelte';

  export let rounds;
  export let minutesForRound;

  const roundsInputLabel = 'Кругов';
  const timeForRoundInputLabel = 'Время круга';
  const minutes = 'минут';
</script>

<div class="settings">
  <form class="settings-form">
    <label class="setting-field" for="rounds">
      {roundsInputLabel}:
      <input
        class="numeric-input"
        id="rounds"
        type="number"
        inputmode="numeric"
        pattern="[0-9]*"
        bind:value={rounds}
        min="1"
      />
    </label>
    <label class="setting-field" for="timeForRound">
      <span class="icon-wrapper" title={timeForRoundInputLabel}>
        <Timer width="30px" height="30px" />
      </span>
      :
      <input
        class="numeric-input"
        id="timeForRound"
        type="number"
        inputmode="numeric"
        pattern="[0-9]*"
        bind:value={minutesForRound}
        min="1"
      />
      {minutes}
    </label>
  </form>
</div>

<style>
  .settings {
    margin-top: 24px;
  }

  .settings-form {
    width: 100%;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
  }

  .setting-field {
    display: flex;
    align-items: center;
  }

  .numeric-input {
    margin: 0 6px;
    min-width: 40px;
    max-width: 50px;
    border: none;
    border-radius: 0;
    border-bottom: 1px solid #000000;
    text-align: center;
    background-color: inherit;
    font-weight: 500;
    font-size: 1.1em;
  }
</style>
