<!--FIRST VERSION-->

<!-- <template>
  <div>
    <input
      v-model="newTask"
      @keyup.enter="addTask"
      placeholder="Add a new task"
    />
    <ul>
      <li v-for="(task, index) in tasks" :key="index">
        {{ task }}
        <button @click="removeTask(index)">Remove</button>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'

export default defineComponent({
  setup() {
    const tasks = ref<string[]>([])
    const newTask = ref('')

    function addTask() {
      if (newTask.value !== '') {
        tasks.value.push(newTask.value)
        newTask.value = ''
      }
    }

    function removeTask(index: number) {
      tasks.value.splice(index, 1)
    }

    return { tasks, newTask, addTask, removeTask }
  }
})
</script> -->

<!--SECOND VERSION Feature 1: Task Prioritization -->

<template>
  <div>
    <!-- Feature 2: Due Dates -->
    <input type="date" v-model="newTask.dueDate" />
    |
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
      <li
        v-for="(task, index) in tasks"
        :key="index"
        :class="taskPriorityClass(task.priority)"
      >
        {{ task.description }}
        <span :style="{ color: priorityColor(task.priority) }">
          {{ task.priority }}
        </span>

        <!-- Feature 2: Due Dates -->
        <span :style="{ color: dueDateColor(task.dueDate) }">
          {{ task.dueDate }}
        </span>

        <button @click="removeTask(index)">Remove</button>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'

// Feature 2: Due Dates //
import { isBefore, isAfter, isToday, parseISO } from 'date-fns'

interface Task {
  description: string
  priority: string
  dueDate: string
}

export default defineComponent({
  setup() {
    const tasks = ref<Task[]>([])
    const newTask = ref<Task>({ description: '', priority: '', dueDate: '' })

    // DEPRECATED FUNCTION //
    // function addTask() {
    //   if (newTask.value.description) {
    //     tasks.value.push({ ...newTask.value })
    //     newTask.value.description = ''
    //     newTask.value.priority = ''
    //   }
    // }

    // UPDATE.1 FUNCTION //
    // function addTask() {
    //   if (
    //     newTask.value.description &&
    //     (!newTask.value.dueDate || isValidFutureDate(newTask.value.dueDate))
    //   ) {
    //     tasks.value.push({ ...newTask.value })
    //     newTask.value.description = ''
    //     newTask.value.priority = ''
    //     newTask.value.dueDate = '' // Reset the due date as well
    //   }
    // }

    // UPDATE.2 FUNCTION //
    function addTask() {
      console.log('Adding task:', newTask.value) // This should show the task being added
      if (
        newTask.value.description &&
        (!newTask.value.dueDate || isValidFutureDate(newTask.value.dueDate))
      ) {
        tasks.value.push({ ...newTask.value })
        console.log('Tasks after adding:', tasks.value) // This should show the tasks array after adding the new task

        // Reset the task
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

    // Feature 2: Due Dates //
    function isOverdue(dueDateString: string) {
      const today = new Date()
      const dueDate = dueDateString ? parseISO(dueDateString) : null
      return dueDate ? isBefore(dueDate, today) : false
    }

    // Feature 2: Due Dates //
    function dueDateColor(dueDateString: string) {
      if (isOverdue(dueDateString)) {
        return 'red'
      }
      return 'inherit'
    }

    // Feature 2: Due Dates //

    // DEPRECATED FUNCTION //
    // function isValidFutureDate(dueDateString: string): boolean {
    //   const today = new Date()
    //   const dueDate = dueDateString ? parseISO(dueDateString) : null
    //   return dueDate ? isAfter(dueDate, today) : false // Ensure the due date is in the future
    // }

    // UPDATED.1 FUNCTION //
    // function isValidFutureDate(dueDateString: string): boolean {
    //   const today = new Date()
    //   today.setHours(0, 0, 0, 1) // Set time just after midnight
    //   const dueDate = dueDateString ? parseISO(dueDateString) : null
    //   return dueDate ? isAfter(dueDate, today) || isToday(dueDate) : false // Ensure the due date is in the future or is today
    // }

    // UPDATED.2 FUNCTION //
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

    return {
      tasks,
      newTask,
      addTask,
      removeTask,
      taskPriorityClass,
      priorityColor,
      dueDateColor
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
  background-color: #1f08ec;
}

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

/* Add margins for visual separation */
li span {
  margin-right: 8px; /* Adjust the value as needed */
}

li button {
  margin-left: 8px; /* Adjust the value as needed */
}
</style>
