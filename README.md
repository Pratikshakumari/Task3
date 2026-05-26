# Task3
# HTML part
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>

  <title>To-Do List App</title>

  <!-- CSS FILE -->
  <link rel="stylesheet" href="style.css">
</head>

<body>

  <div class="todo-container">

    <h1>To-Do List App</h1>

    <!-- INPUT SECTION -->
    <div class="input-section">

      <input
        type="text"
        id="taskInput"
        placeholder="Enter a task..."
      />

      <button id="addTaskBtn">
        Add Task
      </button>

    </div>

    <!-- FILTER BUTTONS -->
    <div class="filters">

      <button class="filter-btn active" data-filter="all">
        All
      </button>

      <button class="filter-btn" data-filter="active">
        Active
      </button>

      <button class="filter-btn" data-filter="completed">
        Completed
      </button>

    </div>

    <!-- TASK LIST -->
    <ul class="task-list" id="taskList"></ul>

  </div>

  <!-- JS FILE -->
  <script src="script.js"></script>

</body>
</html>



# CSS Part
/* =========================
   CSS VARIABLES
========================= */

:root {
  --bg: #f4f4f4;
  --card: #ffffff;
  --text: #222;
  --primary: #2563eb;
  --border: #d1d5db;
}

/* =========================
   GLOBAL STYLES
========================= */

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background: var(--bg);
  color: var(--text);
  min-height: 100vh;

  display: flex;
  justify-content: center;
  align-items: flex-start;

  padding: 40px 20px;
}

.todo-container {
  width: 100%;
  max-width: 600px;
  background: var(--card);
  padding: 25px;
  border-radius: 14px;
  box-shadow: 0 4px 15px rgba(0,0,0,0.1);
}

h1 {
  text-align: center;
  margin-bottom: 20px;
  color: var(--primary);
}

/* =========================
   INPUT SECTION
========================= */

.input-section {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.input-section input {
  flex: 1;
  padding: 12px;
  border: 1px solid var(--border);
  border-radius: 8px;
  font-size: 1rem;
}

.input-section button {
  padding: 12px 18px;
  border: none;
  background: var(--primary);
  color: white;
  border-radius: 8px;
  cursor: pointer;
}

/* =========================
   FILTER BUTTONS
========================= */

.filters {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.filter-btn {
  padding: 8px 14px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  background: #ddd;
}

.filter-btn.active {
  background: var(--primary);
  color: white;
}

/* =========================
   TASK LIST
========================= */

.task-list {
  list-style: none;
}

.task-item {
  background: #fafafa;
  border: 1px solid var(--border);
  border-radius: 10px;
  padding: 12px;

  display: flex;
  justify-content: space-between;
  align-items: center;

  margin-bottom: 12px;
}

.task-content {
  display: flex;
  align-items: center;
  gap: 10px;
  flex: 1;
}

.task-text.completed {
  text-decoration: line-through;
  opacity: 0.6;
}

/* =========================
   ACTION BUTTONS
========================= */

.actions {
  display: flex;
  gap: 8px;
}

.edit-btn,
.delete-btn {
  border: none;
  padding: 8px 10px;
  border-radius: 6px;
  cursor: pointer;
  color: white;
}

.edit-btn {
  background: #10b981;
}

.delete-btn {
  background: #ef4444;
}

/* =========================
   RESPONSIVE DESIGN
========================= */

@media (max-width: 600px) {

  .input-section {
    flex-direction: column;
  }

  .task-item {
    flex-direction: column;
    align-items: flex-start;
    gap: 10px;
  }

  .actions {
    width: 100%;
    justify-content: flex-end;
  }
}





# JS Part

/* =========================
   STATE MANAGEMENT
========================= */

let tasks = JSON.parse(localStorage.getItem("tasks")) || [];

let currentFilter = "all";

const taskInput = document.getElementById("taskInput");
const addTaskBtn = document.getElementById("addTaskBtn");
const taskList = document.getElementById("taskList");
const filterButtons = document.querySelectorAll(".filter-btn");

/* =========================
   SAVE TO LOCAL STORAGE
========================= */

function saveTasks() {
  localStorage.setItem("tasks", JSON.stringify(tasks));
}

/* =========================
   RENDER TASKS
========================= */

function renderTasks() {

  taskList.innerHTML = "";

  let filteredTasks = tasks;

  if (currentFilter === "active") {
    filteredTasks = tasks.filter(task => !task.completed);
  }

  if (currentFilter === "completed") {
    filteredTasks = tasks.filter(task => task.completed);
  }

  filteredTasks.forEach(task => {

    const li = document.createElement("li");

    li.className = "task-item";

    li.innerHTML = `

      <div class="task-content">

        <input
          type="checkbox"
          class="complete-checkbox"
          data-id="${task.id}"
          ${task.completed ? "checked" : ""}
        >

        <span class="task-text ${task.completed ? "completed" : ""}">
          ${task.text}
        </span>

      </div>

      <div class="actions">

        <button
          class="edit-btn"
          data-id="${task.id}"
        >
          Edit
        </button>

        <button
          class="delete-btn"
          data-id="${task.id}"
        >
          Delete
        </button>

      </div>
    `;

    taskList.appendChild(li);

  });

  saveTasks();
}

/* =========================
   ADD TASK
========================= */

function addTask() {

  const text = taskInput.value.trim();

  if (text === "") {
    alert("Please enter a task");
    return;
  }

  const newTask = {
    id: Date.now(),
    text,
    completed: false
  };

  tasks.push(newTask);

  taskInput.value = "";

  renderTasks();
}

addTaskBtn.addEventListener("click", addTask);

/* ENTER KEY SUPPORT */

taskInput.addEventListener("keypress", (e) => {

  if (e.key === "Enter") {
    addTask();
  }

});

/* =========================
   EVENT DELEGATION
========================= */

taskList.addEventListener("click", (e) => {

  const id = Number(e.target.dataset.id);

  /* DELETE */

  if (e.target.classList.contains("delete-btn")) {

    tasks = tasks.filter(task => task.id !== id);

    renderTasks();
  }

  /* EDIT */

  if (e.target.classList.contains("edit-btn")) {

    const task = tasks.find(task => task.id === id);

    const updatedText = prompt(
      "Edit your task:",
      task.text
    );

    if (updatedText !== null && updatedText.trim() !== "") {

      task.text = updatedText.trim();

      renderTasks();
    }
  }

});

/* =========================
   TOGGLE COMPLETE
========================= */

taskList.addEventListener("change", (e) => {

  if (e.target.classList.contains("complete-checkbox")) {

    const id = Number(e.target.dataset.id);

    const task = tasks.find(task => task.id === id);

    task.completed = e.target.checked;

    renderTasks();
  }

});

/* =========================
   FILTERING
========================= */

filterButtons.forEach(button => {

  button.addEventListener("click", () => {

    filterButtons.forEach(btn =>
      btn.classList.remove("active")
    );

    button.classList.add("active");

    currentFilter = button.dataset.filter;

    renderTasks();

  });

});

/* =========================
   INITIAL RENDER
========================= */

renderTasks();
