<template>
    <div>
    <input type="text" v-model="newTaskName" placeholder="" />
    <select v-model="newTaskPriority" class="priority-select">
      <option value="low">Low</option>
      <option value="medium">Medium</option>
      <option value="high">High</option>
    </select>
    <button class="add-btn" @click="addNewTask">ADD</button>
  </div>

  <div>
    <button @click="setActive(true)">Active</button>
    <button @click="setActive(false)">Done</button>
  </div>

<!-- Summary section -->
<div class="summary">
    <div class="summary-box">
        <h3>Pending Tasks: {{ pendingTasks }}</h3>
    </div>
    <div class="summary-box">
        <h3>Low Priority: {{ lowPriorityTasks }}</h3>
    </div>
    <div class="summary-box">
        <h3>Medium Priority: {{ mediumPriorityTasks }}</h3>
    </div>
    <div class="summary-box">
        <h3>High Priority: {{ highPriorityTasks }}</h3>
    </div>
</div>

<!-- Table section -->
<table border="3">
    <thead>
        <tr v-if="showActive">
            <th>ID</th>
            <th>Task</th>
            <th>Priority</th>
            <th>Delete</th>
            <th>Done</th>
        </tr>
    </thead>
    <tbody>
        <template v-for="(task, index) in filteredTasks" :key="index">
            <tr v-if="showActive || task.done">
                <td>{{ task.id }}</td>
                <td>{{ task.name }}</td>
                <td>{{ task.priority }}</td>
                <td v-if="showActive">
                    <button class="del-btn" @click="deleteTask(index)">Delete</button>
                </td>
                <td v-if="showActive">
                    <button class="done-btn" @click="toggleDone" :task="task" :index="index" :showActive="showActive">
                        {{ task.done ? 'Undo' : 'Done' }}
                    </button>
                </td>
            </tr>
        </template>
    </tbody>
</table>
</div>
</template>

<script>
export default {
  props: {
    task: {
      type: Object,
      required: true
    },
    index: {
      type: Number,
      required: true
    },
    showActive: {
      type: Boolean,
      required: true
    }
  },
  name: "TodoApp",
  data() {
    return {
      // Mengganti properti task dan showActive dengan nama yang berbeda
      // Agar tidak bingung dengan properti yang diterima
      newTask: "",
      priority: "low",
      editTask: null,
      tasks: [
        {
          name: "Kerja Tugas 1",
          id: Date.now() + 1,
          done: false,
          priority: "medium",
        },
        {
          name: "Belajar Vue js",
          id: Date.now() + 2,
          done: false,
          priority: "low",
        },
        {
          name: "Error dan Debugging",
          id: Date.now() + 3,
          done: false,
          priority: "high",
        },
      ],
      isActive: true, // Menggunakan nama yang berbeda agar tidak sama dengan properti showActive
      lowPriorityTasks: 1,
      mediumPriorityTasks: 1,
      highPriorityTasks: 1,
    };
  },
    computed: {
    pendingTasks() {
        return this.tasks.filter((task) => !task.done).length;
    },
    filteredTasks() {
        return this.showActive
        ? this.tasks.filter((task) => !task.done)
        : this.tasks.filter((task) => task.done);
    },
    },
    methods: {
    toggleDone() {
      const task = this.tasks[this.index];
      const wasDone = task.done;

      // Mengubah status task (Done/Undone)
      task.done = !wasDone;

      // Update priority counts based on task movement
      if (wasDone) {
        // Task dipindahkan dari Done ke Active
        if (task.priority === "low") {
          this.lowPriorityTasks++;
        } else if (task.priority === "medium") {
          this.mediumPriorityTasks++;
        } else if (task.priority === "high") {
          this.highPriorityTasks++;
        }
      } else {
        // Task dipindahkan dari Active ke Done
        if (task.priority === "low") {
          this.lowPriorityTasks--;
        } else if (task.priority === "medium") {
          this.mediumPriorityTasks--;
        } else if (task.priority === "high") {
          this.highPriorityTasks--;
        }
      }
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
    },
    submitTask() {
      if (this.newTask.length === 0) {
        return;
      }
      if (this.editTask !== null && this.editTask !== undefined) {
        this.tasks[this.editTask].name = this.newTask;
        this.tasks[this.editTask].priority = this.priority;
        this.editTask = null;
      } else {
        this.tasks.push({
          name: this.newTask,
          id: Date.now() + 4,
          done: false,
          priority: this.priority,
        });
        if (this.priority === "low") {
          this.lowPriorityTasks++;
        } else if (this.priority === "medium") {
          this.mediumPriorityTasks++;
        } else if (this.priority === "high") {
          this.highPriorityTasks++;
        }
      }
      this.newTask = "";
      this.priority = "low";
    },
},
};
</script>

<style scoped>
.container {
    width: 600px;
    margin: auto;
}

table {
    font-family: arial, sans-serif;
    border-collapse: collapse;
    width: 100%;
    margin-top: 20px;
}

td,
    th {
    border: 1px solid #dddddd;
    text-align: center;
    padding: 8px;
}

    tr:nth-child(even) {
    background-color: #dddddd;
}
.add-btn {
    border: none;
    width: 100px;
    height: 30px;
    padding: 2px;
    background-color: teal;
    color: white;
}
.del-btn {
    border: none;
    background-color: red;
    color: white;
}

.done-btn {
    border: none;
    background-color: #77b631;
    color: white;
}
.priority-select {
    margin-left: 10px;
}
</style>
