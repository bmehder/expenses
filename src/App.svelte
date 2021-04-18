<script>
  import { setContext, onMount, afterUpdate } from "svelte";

  import Navbar from "./Navbar.svelte";
  import ExpensesList from "./ExpensesList.svelte";
  import Totals from "./Totals.svelte";
  import ExpenseForm from "./ExpenseForm.svelte";

  /**
   * Local vars
   */

  let expenses = [];
  let isFormOpen = false;

  let selectedId = null;
  let selectedName = "";
  let selectedAmount = null;

  /**
   * Predicates
   */

  $: isEditing = selectedId ? true : false;

  $: isLocalStorageHasExpenses = localStorage.getItem("expenses");

  /**
   * CRUD Functions
   */

  const addExpense = ({ name, amount }) => {
    const id = Math.random() * Date.now();
    expenses = [{ id, name, amount }, ...expenses];
  };

  const removeExpense = (id) => {
    expenses = expenses.filter((item) => item.id !== id);
  };

  const selectExpense = (id) => {
    let expense = expenses.find((item) => item.id === id);

    selectedId = expense.id;
    selectedName = expense.name;
    selectedAmount = expense.amount;

    showForm();
  };

  const updateExpense = ({ name, amount }) => {
    expenses = expenses.map((item) => {
      return item.id === selectedId ? { ...item, name, amount } : { ...item };
    });

    resetForm();
  };

  /**
   * Helper Functions
   */

  // reactively calculate total expense
  $: total = expenses.reduce((acc, curr) => {
    return (acc += curr.amount);
  }, 0);

  const showForm = () => (isFormOpen = true);

  const hideForm = () => resetForm() && (isFormOpen = false);

  const resetForm = () => {
    selectedName = "";
    selectedAmount = null;
    selectedId = null;

    return true;
  };

  const clearExpenses = () => {
    const isConfirmed = confirm("Are you sure you want to delete everything?");
    isConfirmed && (expenses = []);
  };

  /**
   * Local Storage functions
   */

  const setLocalStorage = () =>
    localStorage.setItem("expenses", JSON.stringify(expenses));

  const getExpensesFromLocalStorage = () =>
    JSON.parse(localStorage.getItem("expenses"));

  const getLocalStorage = () => {
    expenses = isLocalStorageHasExpenses ? getExpensesFromLocalStorage() : [];
  };

  /**
   * Life Cycle functions
   */

  onMount(() => getLocalStorage());
  afterUpdate(() => setLocalStorage());

  /**
   * Global State
   *
   * used to export functions directly to deeply nested components
   */

  setContext("remove", removeExpense);
  setContext("modify", selectExpense);
</script>

<Navbar {showForm} />

<main class="content">
  {#if isFormOpen}
    <ExpenseForm
      {addExpense}
      name={selectedName}
      amount={selectedAmount}
      {isEditing}
      {updateExpense}
      {hideForm}
    />
  {/if}

  <Totals title="total expenses" {total} />

  <ExpensesList {expenses} />

  {#if expenses.length > 0}
    <button
      type="button"
      class="btn bt-primary btn-block"
      on:click={clearExpenses}>clear expenses</button
    >
  {/if}
</main>
