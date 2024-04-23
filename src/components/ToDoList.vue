<!--THIRD VERSION Feature 3: Task Editing -->

<template>
  <!--DEPRECATED
  <button @click="isDarkMode = !isDarkMode">Toggle Dark Mode</button>-->
  <!--Feature 4: Task Dark mode-->

  <!--UPDATED-->
  <button @click="toggleDarkMode">Toggle Dark Mode</button>

  <div :class="{ 'dark-mode': isDarkMode }">
    <!--Feature 4: Task Dark mode -->
    <div>
      <input type="date" v-model="newTask.dueDate" />
      <input
        v-model="newTask.description"
        placeholder="Add a new task"
        @keyup.enter="addTask"
      />

      <select v-model="newTask.priority">
        <option disabled value="">Priority</option>
        <option value="high">High</option>
        <option value="medium">Medium</option>
        <option value="low">Low</option>
      </select>
      <ul>
        <!--THIRD VERSION Feature 3: Task Editing -->
        <li
          v-for="(task, index) in tasks"
          :key="index"
          :class="taskPriorityClass(task.priority)"
        >
          <div v-if="task.isEditing">
            <input
              v-model="task.description"
              @keyup.enter="finishEditing(task)"
            />
            <input type="date" v-model="task.dueDate" />
            <select v-model="task.priority">
              <option value="high">High</option>
              <option value="medium">Medium</option>
              <option value="low">Low</option>
            </select>
            <button @click="finishEditing(task)">Done</button>
          </div>
          <div v-else>
            <!-- Display task properties -->
            <span @click="editTask(task)">{{ task.description }}</span>
            <span :style="{ color: priorityColor(task.priority) }">
              {{ task.priority }}
            </span>
            <span :style="{ color: dueDateColor(task.dueDate) }">
              {{ task.dueDate }}
            </span>
            <button @click="editTask(task)">Edit</button>
            <button @click="removeTask(index)">Remove</button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import { isBefore, isAfter, isToday, parseISO } from 'date-fns'

interface Task {
  description: string
  priority: string
  dueDate: string
  isEditing: boolean
}

export default defineComponent({
  setup() {
    const tasks = ref<Task[]>([])
    const newTask = ref<Task>({
      description: '',
      priority: '',
      dueDate: '',
      isEditing: true
    })
    const isDarkMode = ref(false)

    const toggleDarkMode = () => {
      isDarkMode.value = !isDarkMode.value
      if (isDarkMode.value) {
        document.body.classList.add('dark-mode')
      } else {
        document.body.classList.remove('dark-mode')
      }
    }

    function addTask() {
      console.log('Adding task:', newTask.value)
      if (
        newTask.value.description &&
        (!newTask.value.dueDate || isValidFutureDate(newTask.value.dueDate))
      ) {
        tasks.value.push({ ...newTask.value, isEditing: false })
        console.log('Tasks after adding:', tasks.value)

        newTask.value.description = ''
        newTask.value.priority = ''
        newTask.value.dueDate = ''
      } else {
        console.error(
          'Task not added. Description:',
          newTask.value.description,
          'Due date:',
          newTask.value.dueDate,
          'Due date validity:',
          isValidFutureDate(newTask.value.dueDate)
        )
      }
    }

    function removeTask(index: number) {
      tasks.value.splice(index, 1)
    }

    function taskPriorityClass(priority: string) {
      return {
        'high-priority': priority === 'high',
        'medium-priority': priority === 'medium',
        'low-priority': priority === 'low'
      }
    }

    function priorityColor(priority: string) {
      switch (priority) {
        case 'high':
          return 'red'
        case 'medium':
          return 'orange'
        case 'low':
          return 'green'
        default:
          return 'black'
      }
    }

    function isOverdue(dueDateString: string) {
      const today = new Date()
      const dueDate = dueDateString ? parseISO(dueDateString) : null
      return dueDate ? isBefore(dueDate, today) : false
    }

    function dueDateColor(dueDateString: string) {
      if (isOverdue(dueDateString)) {
        return 'red'
      }
      return 'inherit'
    }

    function isValidFutureDate(dueDateString: string): boolean {
      const today = new Date()
      today.setHours(0, 0, 0, 1) // Set time just after midnight
      console.log('Today:', today.toISOString())

      const dueDate = dueDateString ? parseISO(dueDateString) : null
      console.log('Due Date:', dueDate?.toISOString())

      if (dueDate) {
        console.log('Is After Today:', isAfter(dueDate, today))
        console.log('Is Today:', isToday(dueDate))
      }
      return dueDate ? isAfter(dueDate, today) || isToday(dueDate) : false
    }

    function editTask(task: Task) {
      task.isEditing = true
    }

    function finishEditing(task: Task) {
      task.isEditing = false
      if (task.dueDate && !isValidFutureDate(task.dueDate)) {
        task.dueDate = '' // Reset due date if it's invalid after editing
      }
    }

    return {
      tasks,
      newTask,
      isDarkMode, ///Feature 4: Task Dark mode ///
      toggleDarkMode, ///Feature 4: Task Dark mode ///
      addTask,
      removeTask,
      taskPriorityClass,
      priorityColor,
      dueDateColor,
      editTask,
      finishEditing
    }
  }
})
</script>

<style>
.high-priority {
  font-weight: bold;
}

.medium-priority {
  font-style: italic;
}

.low-priority {
  opacity: 0.8;
}

.overdue {
  background-color: #ffcccc;
}

li span {
  margin-right: 8px;
}

li button {
  margin-left: 8px;
}

/* Global dark mode styles */
body.dark-mode {
  background-color: #333;
  color: #fff;
}

body.dark-mode #app {
  background-color: #333;
  color: #fff;
}

body.dark-mode #app li {
  border-color: #fff;
}
</style>
