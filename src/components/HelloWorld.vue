<script setup lang="ts">
import ClickCounter from './ClickCounter.vue'
import ItemList from './ItemList.vue'
import TodoList from './TodoList.vue'

defineProps<{
  msg: string
}>()
</script>

<template>
  <div>
    <h1>{{ msg }}</h1>
    <ClickCounter />
    <ItemList />
    <TodoList />
  </div>
</template>

<style scoped>
a {
  color: #42b983;
}
</style>