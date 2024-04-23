<template>
  <div id="app">
    <h1>My To-Do List</h1>
    <ToDoList />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import ToDoList from './components/ToDoList.vue'

export default defineComponent({
  name: 'App',
  components: {
    ToDoList
  }
})
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
