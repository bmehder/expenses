<script>
  import Title from "./Title.svelte";
  import Modal from "./Modal.svelte";

  export let name = "";
  export let amount = null;
  export let isEditing;
  export let addExpense;
  export let updateExpense;
  export let hideForm;

  $: isEmpty = !name || !amount;

  const resetForm = () => {
    name = "";
    amount = "";
  };
  function handleSubmit() {
    isEditing ? updateExpense({ name, amount }) : addExpense({ name, amount });

    resetForm();
    hideForm();
  }
</script>

<Modal>
  <section class="form">
    <Title title="add expense" />

    <form class="expense-form" on:submit|preventDefault={handleSubmit}>
      <div class="form-control">
        <label for="name">name</label>
        <input type="text" id="name" bind:value={name} />
      </div>

      <div class="form-control">
        <label for="amount">amount</label>
        <input type="number" step="any" id="amount" bind:value={amount} />
      </div>

      {#if isEmpty}
        <p class="form-empty">please fill out all form fields</p>
      {/if}

      <button
        type="submit"
        class="btn btn-block"
        disabled={isEmpty}
        class:disabled={isEmpty}
      >
        {#if isEditing}
          edit expense
        {:else}
          add expense
        {/if}
      </button>

      <button type="button" class="close-btn" on:click={hideForm}>
        <i class="fas fa-times" /> close
      </button>
    </form>
  </section>
</Modal>
