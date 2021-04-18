<script>
  import { fly, fade } from "svelte/transition";
  import { flip } from "svelte/animate";
  import SectionTitle from "./Title.svelte";
  import Expense from "./Expense.svelte";

  export let expenses = [];
</script>

<section>
  <SectionTitle title="expense list" />
  <ul>
    {#each expenses as expense, index (expense.id)}
      <div
        in:fly={{ x: 200, delay: 400 * (index + 1) }}
        out:fly={{ x: -200 }}
        animate:flip
      >
        <Expense {...expense} />
      </div>
    {:else}
      <h2 in:fade={{ delay: 1000, duration: 200 }}>There are no expenses</h2>
    {/each}
  </ul>
</section>

<style>
  h2 {
    text-transform: capitalize;
  }
</style>
