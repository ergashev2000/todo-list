<template>
  <EditTaskModal v-if="isEditing" :task-text="editedTaskText" @edit-task="saveEditedTask" />

  <div class="wrapper">
    <h2>To-Do List</h2>

    <!-- Add Task -->
    <div class="add-task">
      <input v-model="newTask" @keyup.enter="addTask" placeholder="Add a new task" />
      <button @click="addTask">Add</button>
    </div>

    <div class="filter-search-box">
      <!-- Search -->
      <div class="search">
        <input v-model="searchTerm" placeholder="Search tasks" />
      </div>

      <!-- Filter -->
      <div class="filter">
        <label>Filter: </label>
        <select v-model="filter" @change="filterTasks">
          <option value="all">All</option>
          <option value="completed">Completed</option>
          <option value="uncompleted">Uncompleted</option>
        </select>
      </div>
    </div>

    <!-- Tasks List -->
    <ul class="task-list">
      <li v-for="(task, index) in filteredTasks" :key="index">
        <div>
          <input type="checkbox" v-model="task.completed" class="checkbox" />
          <span :class="{ 'completed-task': task.completed }">{{ task.text }}</span>
        </div>
        <div class="task-list-btns">
          <button @click="openEditModal(index)">Edit</button>
          <button @click="deleteTask(index)">Delete</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, computed, onMounted, watch } from 'vue'
import EditTaskModal from './components/Modal.vue'

export default {
  components: {
    EditTaskModal
  },
  setup() {
    const newTask = ref('')
    const tasks = ref([])
    const filter = ref(localStorage.getItem('filter') || 'all')
    const searchTerm = ref('')
    const editedTaskIndex = ref(null)
    const editedTaskText = ref('')
    const isEditing = ref(false)

    const filteredTasks = computed(() => {
      return tasks.value
        .filter((task) => {
          if (filter.value === 'all') {
            return true
          } else if (filter.value === 'completed') {
            return task.completed
          } else {
            return !task.completed
          }
        })
        .filter((task) => {
          return task.text?.toLowerCase()?.includes(searchTerm.value?.toLowerCase())
        })
    })

    const addTask = () => {
      if (newTask.value.trim() === '') return
      tasks.value.push({ text: newTask.value, completed: false })
      newTask.value = ''
      saveTasks()
    }

    const openEditModal = (index) => {
      editedTaskText.value = tasks.value[index].text
      editedTaskIndex.value = index
      isEditing.value = true
    }

    const saveEditedTask = (editedText) => {
      if (editedTaskIndex.value !== null) {
        tasks.value[editedTaskIndex.value].text = editedText
        saveTasks()
      }
      isEditing.value = false
      editedTaskIndex.value = null
    }

    const deleteTask = (index) => {
      tasks.value.splice(index, 1)
      saveTasks()
    }

    const filterTasks = () => {
      localStorage.setItem('filter', filter.value)
      saveTasks()
    }

    const saveTasks = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value))
    }

    const loadTasks = () => {
      const savedTasks = localStorage.getItem('tasks')
      if (savedTasks) {
        tasks.value = JSON.parse(savedTasks)
      }
    }

    onMounted(() => {
      loadTasks()
    })

    watch(tasks, () => {
      saveTasks()
    })

    return {
      newTask,
      tasks,
      filter,
      searchTerm,
      filteredTasks,
      isEditing,
      editedTaskText,
      addTask,
      openEditModal,
      saveEditedTask,
      deleteTask,
      filterTasks
    }
  }
}
</script>

<style scoped>
.wrapper {
  background: #fff;
  max-width: 600px;
  width: 100%;
  border-radius: 10px;
  margin: 100px auto;
  padding: 30px;
  box-shadow:
    0 1px 3px 0 rgb(0 0 0 / 0.1),
    0 1px 2px -1px rgb(0 0 0 / 0.1);
}
.wrapper > h2 {
  margin-bottom: 10px;
}
.completed-task {
  text-decoration: line-through;
}
.add-task {
  margin-bottom: 10px;
  display: flex;
  gap: 10px;
  width: 100%;
}
.add-task input {
  padding: 8px 15px;
  border-radius: 5px;
  outline: none;
  border: none;
  width: 100%;
  box-shadow:
    0 1px 3px 0 rgb(0 0 0 / 0.1),
    0 1px 2px -1px rgb(0 0 0 / 0.1);
}

.add-task button {
  padding: 10px 30px;
  border-radius: 5px;
  background: green;
  outline: none;
  border: none;
  color: #fff;
  cursor: pointer;
}

.filter-search-box {
  display: flex;
  gap: 20px;
  align-items: center;
  justify-content: space-between;
  padding-top: 10px;
}
.filter select {
  padding: 5px 10px;
  border-radius: 5px;
}
.filter label {
  font-size: 20px;
  font-weight: 500;
}
.search input {
  padding: 8px 10px;
  border-radius: 5px;
  outline: none;
  border: none;
  width: 100%;
  box-shadow:
    0 1px 3px 0 rgb(0 0 0 / 0.1),
    0 1px 2px -1px rgb(0 0 0 / 0.1);
}
.task-list {
  padding-top: 20px;
}
.task-list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 10px;
  padding: 3px  0px 3px 30px;
  position: relative;
}

.task-list-btns button {
  outline: none;
  padding: 5px 15px;
  border: none;
  border-radius: 5px;
  background-color: green;
  color: #fff;
  font-weight: 500;
  cursor: pointer;
  transition: all .3s;
}
.task-list-btns button:hover{
  opacity: .7;
}

.task-list-btns button:active{
  opacity: 1;
}

.task-list-btns button:nth-child(1) {
  background-color: red;
  margin-right: 10px;
}

.checkbox {
  appearance: none;
}

.checkbox::before {
  content: '';
  position: absolute;
  left: -2px;
  top: 50%;
  transform: translateY(-50%);
  width: 20px;
  height: 20px;
  background: red;
  border-radius: 100%;
  border: 1px solid #000;
  background: transparent;
  cursor: pointer;
}
input[type='checkbox']:checked::before {
  background-image: url(./assets/check.svg);
  background-size: contain;
}

input[type='checkbox']:checked + span {
  color: #00000050;
}
</style>
