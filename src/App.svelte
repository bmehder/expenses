<script>
  import { setContext, onMount, afterUpdate } from "svelte";
  // import expensesData from "./expenses";
  import Navbar from "./Navbar.svelte";
  import ExpensesList from "./ExpensesList.svelte";
  import Totals from "./Totals.svelte";
  import ExpenseForm from "./ExpenseForm.svelte";
  import Modal from "./Modal.svelte";

  /**
   * Local vars
   */

  let expenses = [];
  let setId = null;
  let setName = "";
  let setAmount = null;
  let isFormOpen = false;

  /**
   * Predicates
   */

  $: isEditing = setId ? true : false;

  $: isLocalStorageHasExpenses = localStorage.getItem("expenses");

  /**
   * CRUD Functions
   */

  const addExpense = ({ name, amount }) => {
    let expense = {
      id: Math.random() * Date.now(),
      name,
      amount,
    };
    expenses = [expense, ...expenses];
  };

  const removeExpense = (id) => {
    expenses = expenses.filter((item) => item.id !== id);
  };

  const selectExpense = (id) => {
    let expense = expenses.find((item) => item.id === id);

    setId = expense.id;
    setName = expense.name;
    setAmount = expense.amount;

    showForm();
  };

  const updateExpense = ({ name, amount }) => {
    expenses = expenses.map((item) => {
      return item.id === setId ? { ...item, name, amount } : { ...item };
    });
    resetForm();
  };

  /**
   * Helper Functions
   */

  $: total = expenses.reduce((acc, curr) => {
    return (acc += curr.amount);
  }, 0);

  const showForm = () => (isFormOpen = true);

  const hideForm = () => {
    isFormOpen = false;
    resetForm();
  };

  const resetForm = () => {
    setName = "";
    setAmount = null;
    setId = null;
  };

  const clearExpenses = () => {
    const isConfirmed = confirm("Are you sure you want to delete everything?");
    isConfirmed && (expenses = []);
  };

  /**
   * Local Storage & Life-cycles
   */

  const setLocalStorage = () =>
    localStorage.setItem("expenses", JSON.stringify(expenses));

  const getExpensesFromLocalStorage = () =>
    JSON.parse(localStorage.getItem("expenses"));

  const getLocalStorage = () => {
    expenses = isLocalStorageHasExpenses ? getExpensesFromLocalStorage() : [];
  };

  onMount(() => getLocalStorage());
  afterUpdate(() => setLocalStorage());

  /**
   * Global State
   */

  setContext("remove", removeExpense);
  setContext("modify", selectExpense);
</script>

<Navbar {showForm} />

<main class="content">
  {#if isFormOpen}
    <Modal>
      <ExpenseForm
        {addExpense}
        name={setName}
        amount={setAmount}
        {isEditing}
        {updateExpense}
        {hideForm}
      />
    </Modal>
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
