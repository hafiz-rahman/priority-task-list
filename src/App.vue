<script>
export default {
  data() {
    return {
      newTask: { name: "", priority: "" },
      tasks: JSON.parse(localStorage.getItem("tasks")) || [],
    };
  },
  computed: {
    sortedTasks() {
      const groupedTasks = { High: [], Medium: [], Low: [] };
      this.tasks.forEach((task) => groupedTasks[task.priority].push(task));
      Object.keys(groupedTasks).forEach((key) => {
        groupedTasks[key].sort((a, b) => a.name.localeCompare(b.name));
      });
      return groupedTasks;
    },
  },
  methods: {
    addTask() {
      if (this.newTask.name && this.newTask.priority) {
        this.tasks.push({ ...this.newTask, completed: false });
        this.saveTasks();
        this.newTask = { name: "", priority: "" };
      }
    },
    toggleComplete(task) {
      task.completed = !task.completed;
      this.saveTasks();
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks();
    },
    saveTasks() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
  },
  watch: {
    tasks: {
      handler() {
        this.saveTasks();
      },
      deep: true,
    },
  },
};
</script>

<template>
  <div id="app">
    <h1>Priority Task List</h1>
    <form @submit.prevent="addTask" class="task-form">
      <div class="form-group">
        <input
          v-model="newTask.name"
          placeholder="Task Name"
          required
          class="input-field"
        />
      </div>
      <div class="form-group">
        <select v-model="newTask.priority" required class="input-field">
          <option disabled value="">Select Priority</option>
          <option value="High">High</option>
          <option value="Medium">Medium</option>
          <option value="Low">Low</option>
        </select>
      </div>
      <button type="submit" class="add-task-btn">ADD TASK</button>
    </form>

    <!-- Priority Groups -->
    <div v-for="priority in ['High', 'Medium', 'Low']" :key="priority" class="priority-group">
      <h2 :class="'priority-' + priority.toLowerCase()">{{ priority }} Priority</h2>
      <ul>
        <li
          v-for="(task, index) in sortedTasks[priority]"
          :key="task.name"
          :class="{ completed: task.completed }"
        >
          <span>{{ task.name }}</span>
          <div class="task-buttons">
            <button @click="toggleComplete(task)" class="complete-btn">
              {{ task.completed ? 'Undo' : 'Complete' }}
            </button>
            <button @click="deleteTask(index)" class="delete-btn">Delete</button>
          </div>
        </li>
      </ul>
      <p v-if="!sortedTasks[priority].length" class="no-tasks">
        No tasks available in this category
      </p>
    </div>
  </div>
</template>

<style>
/* General Body and App Styling */
body {
  font-family: 'Arial', sans-serif;
  background-color: #f4f4f9;
  color: #333;
  margin: 0;
  padding: 20px;
}

#app {
  max-width: 500px;
  margin: 0 auto;
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  text-align: center;
}

/* Header */
h1 {
  color: #4a4e69;
  margin-bottom: 20px;
}

/* Form Styling */
.task-form {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
}

.form-group {
  width: 100%;
}

.input-field {
  width: 100%;
  padding: 10px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
}

.add-task-btn {
  background-color: #6a4c93;
  color: #fff;
  border: none;
  padding: 10px;
  font-size: 1rem;
  border-radius: 5px;
  cursor: pointer;
  width: 100%;
  transition: background-color 0.3s ease;
}

.add-task-btn:hover {
  background-color: #5a3c83;
}

/* Priority Group Styling */
.priority-group {
  margin-top: 20px;
  text-align: left;
}

.priority-group h2 {
  color: #fff;
  padding: 10px;
  border-radius: 5px;
  text-align: center;
}

.priority-high {
  background-color: #e63946;
}

.priority-medium {
  background-color: #f4a261;
}

.priority-low {
  background-color: #2a9d8f;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  background-color: #f4f4f9;
  margin: 5px 0;
  padding: 10px;
  border-radius: 5px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.completed {
  text-decoration: line-through;
  color: #777;
}

.task-buttons {
  display: flex;
  gap: 10px;
}

.complete-btn,
.delete-btn {
  padding: 6px 12px;
  font-size: 0.9rem;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-left: 10px;
}

.complete-btn {
  background-color: #4caf50;
  color: white;
}

.complete-btn:hover {
  background-color: #388e3c;
}

.delete-btn {
  background-color: #e53935;
  color: white;
}

.delete-btn:hover {
  background-color: #c62828;
}

.no-tasks {
  color: #777;
  font-style: italic;
  text-align: center;
  margin-top: 10px;
}
</style>
