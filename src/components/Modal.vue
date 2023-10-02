<template>
  <div class="modal" v-if="isModalOpen">
    <div class="modal-content">
      <h3>Edit Task</h3>
      <input v-model="editedTask" @keyup.enter="saveEditedTask" />
      <div class="button-container">
        <button @click="saveEditedTask">Save</button>
        <button @click="closeModal">Cancel</button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue'

export default {
  props: ['taskText'],
  emits: ['edit-task'],

  setup(props, { emit }) {
    const editedTask = ref(props.taskText)
    const isModalOpen = ref(true)

    const saveEditedTask = () => {
      emit('edit-task', editedTask.value)
      closeModal()
    }

    const closeModal = () => {
      editedTask.value = props.taskText
      isModalOpen.value = false
    }

    watch(props, () => {
      editedTask.value = props.taskText
    })

    return {
      editedTask,
      isModalOpen,
      saveEditedTask,
      closeModal
    }
  }
}
</script>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background: #fff;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.modal h3 {
  margin: 0 0 10px;
}

.modal input {
  width: 100%;
  padding: 8px;
  margin-bottom: 10px;
}

.button-container {
  display: flex;
  justify-content: flex-end;
}

.modal button {
  background-color: green;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 5px;
  cursor: pointer;
  margin-left: 10px;
  cursor: pointer;
}

.modal button:last-child {
  margin-left: 10px;
  background-color: rgba(0, 0, 255, 0.578);
}
</style>
