<script>
  import { onMount } from 'svelte';
  import axios from 'axios';

  let todos = [];
  let newTodo = '';

  // Hämta alla todos från API:et
  const fetchTodos = async () => {
    const response = await axios.get('http://localhost:3000/todos');
    todos = response.data;
  };

  // Lägg till en ny todo
  const addTodo = async () => {
    if (newTodo.trim() === '') return;

    const response = await axios.post('http://localhost:3000/todos', {
      todo: newTodo,
      done: false,
    });

    todos = [...todos, response.data];
    newTodo = '';
  };

  // Markera todo som klar
  const markDone = async (id) => {
    const todo = todos.find((t) => t.id === id);
    if (!todo || todo.done) return;

    await axios.put(`http://localhost:3000/todos/${id}`, {
      ...todo,
      done: true,
    });

    todos = todos.map((t) => (t.id === id ? { ...t, done: true } : t));
  };

  // Hämta todos vid sidladdning
  onMount(fetchTodos);
</script>

<style>
  h1 {
    text-align: center;
    font-size: 2rem;
  }
  .todo-list {
    list-style-type: none;
    padding: 0;
  }
  .todo-item {
    display: flex;
    justify-content: space-between;
    padding: 10px;
    margin: 5px 0;
    border: 1px solid #ddd;
  }
  .done {
    text-decoration: line-through;
    color: gray;
  }
</style>

<main>
  <h1>Todo - Just do it!</h1>

  <!-- Form för att lägga till ny Todo -->
  <form on:submit|preventDefault={addTodo}>
    <input type="text" bind:value={newTodo} placeholder="Ny uppgift" />
    <button type="submit">Lägg till</button>
  </form>

  <!-- Lista av todos -->
  <ul class="todo-list">
    {#each todos as todo (todo.id)}
      <li class="todo-item">
        <span class={todo.done ? 'done' : ''}>{todo.todo}</span>
        <button on:click={() => markDone(todo.id)} disabled={todo.done}>
          ✅
        </button>
      </li>
    {/each}
  </ul>
</main>
