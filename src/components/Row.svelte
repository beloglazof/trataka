<script>
  import { blur } from 'svelte/transition';

  export let words;
  export let wordDelay;
</script>

{#if words.length > 0}
  {#each words as word, index}
    <span class="word" in:blur={{ duration: wordDelay / 2, delay: (wordDelay * index) / 3 }}>
      {word}
    </span>
  {/each}
{/if}

<style>
  .word {
    font-family: 'Brygada 1918', serif;
    font-size: 50px;
  }

  @media (max-width: 499px) {
    .word {
      font-size: 38px;
    }
  }

  @media (max-width: 349px) {
    .word {
      font-size: 30px;
    }
  }
</style>
