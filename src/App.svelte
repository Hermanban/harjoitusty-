<script>
  import { writable } from 'svelte/store';

  // Store tehtäville
  export const tasks = writable([]);

  let newTask = '';
  let newDueDate = '';

  // Lisää uusi tehtävä
  function addTask() {
    if (newTask.trim() !== '' && newDueDate.trim() !== '') {
      tasks.update((t) => [
        ...t,
        {
          text: newTask,
          completed: false,
          createdAt: new Date(),
          dueDate: new Date(newDueDate),
          notes: '',
        },
      ]);
      newTask = '';
      newDueDate = '';
    }
  }

  // Poista tehtävä
  function removeTask(index) {
    tasks.update((t) => t.filter((_, i) => i !== index));
  }

  // Vaihda tehtävän tila (tehty/tehtävä)
  function toggleTask(index) {
    tasks.update((t) => {
      const updatedTasks = [...t];
      updatedTasks[index].completed = !updatedTasks[index].completed;
      return updatedTasks;
    });
  }

  // Päivitä tehtävän muistiinpanot
  function updateNotes(index, notes) {
    tasks.update((t) => {
      const updatedTasks = [...t];
      updatedTasks[index].notes = notes;
      return updatedTasks;
    });
  }
</script>

<main>
  <h1>Tehtävienhallintasovellus</h1>

  <form on:submit|preventDefault={addTask}>
    <input type="text" placeholder="Kirjoita tehtävä" bind:value={newTask} />
    <input
      type="date"
      name="eräpäivä"
      placeholder="Valitse eräpäivä"
      bind:value={newDueDate}
    />
    <button disabled={!newTask.trim() || !newDueDate.trim()}>Lisää</button>
  </form>

  {#if $tasks.length > 0}
    <ul>
      {#each $tasks as task, index}
        <li class="task">
          <div class="task-header">
            <div class="task-info">
              <span class:completed={task.completed}>{task.text}</span>
              <span class="task-time"
                >Luotu: {task.createdAt.toLocaleString()}</span
              >
              <span class="task-time"
                >Eräpäivä: {task.dueDate.toLocaleDateString()}</span
              >
            </div>
            <div>
              <button on:click={() => toggleTask(index)}>
                {task.completed ? 'Palauta' : 'Tehty'}
              </button>
              <button on:click={() => removeTask(index)}>Poista</button>
            </div>
          </div>
          <textarea
            class="notes"
            placeholder="Kirjoita muistiinpanoja tähän..."
            bind:value={task.notes}
            on:input={(e) => updateNotes(index, e.target.value)}
          ></textarea>
        </li>
      {/each}
    </ul>
  {:else}
    <p>Ei tehtäviä. Lisää uusi tehtävä yllä olevasta lomakkeesta!</p>
  {/if}
</main>

<style>
  .completed {
    text-decoration: line-through;
    color: gray;
  }

  .task {
    display: flex;
    flex-direction: column;
    margin: 10px 0;
  }

  .task-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .task-info {
    display: flex;
    flex-direction: column;
  }

  .task-time {
    font-size: 0.8rem;
    color: darkgray;
  }

  .notes {
    margin-top: 5px;
    font-size: 0.9rem;
    font-style: italic;
  }
</style>
