<script>
  import { setContext, onMount, afterUpdate } from "svelte";
  // import expensesData from "./expenses";
  import Navbar from "./Navbar.svelte";
  import ExpensesList from "./ExpensesList.svelte";
  import Totals from "./Totals.svelte";
  import ExpenseForm from "./ExpenseForm.svelte";
  import Modal from "./Modal.svelte";

  let expenses = [];
  let setId = null;
  let setName = "";
  let setAmount = null;
  let isFormOpen = false;

  $: isEditing = setId ? true : false;

  $: total = expenses.reduce((acc, curr) => {
    return (acc += curr.amount);
  }, 0);

  $: isLocalStorage = localStorage.getItem("expenses");

  const showForm = () => (isFormOpen = true);

  const hideForm = () => {
    isFormOpen = false;
    resetForm();
  };

  function resetForm() {
    setName = "";
    setAmount = null;
    setId = null;
  }

  function removeExpense(id) {
    expenses = expenses.filter((item) => item.id !== id);
  }

  function addExpense({ name, amount }) {
    let expense = {
      id: Math.random() * Date.now(),
      name,
      amount,
    };
    expenses = [expense, ...expenses];
  }

  function setModifiedExpense(id) {
    let expense = expenses.find((item) => item.id === id);

    setId = expense.id;
    setName = expense.name;
    setAmount = expense.amount;

    showForm();
  }

  function editExpense({ name, amount }) {
    expenses = expenses.map((item) => {
      return item.id === setId ? { ...item, name, amount } : { ...item };
    });
    resetForm();
  }

  function clearExpenses() {
    const isConfirmed = confirm("Are you sure you want to delete everything?");
    isConfirmed && (expenses = []);
  }

  const setLocalStorage = () =>
    localStorage.setItem("expenses", JSON.stringify(expenses));

  setContext("remove", removeExpense);
  setContext("modify", setModifiedExpense);

  onMount(() => {
    expenses = isLocalStorage
      ? JSON.parse(localStorage.getItem("expenses"))
      : [];
  });

  afterUpdate(() => setLocalStorage());
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
        {editExpense}
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
