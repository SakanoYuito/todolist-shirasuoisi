<script setup lang="ts">
import { computed } from '@vue/reactivity';
import { ref } from 'vue'

interface Task {
    name: string,
    done: boolean,
}

const tasks = ref<Task[]>([
    { name: 'ToDoリストをつくる', done: true }
])


const newTaskName = ref('')

const addTask = () => {
    if (newTaskName.value != '') {
        tasks.value.push({ name: newTaskName.value, done: false })
        newTaskName.value = ''
    }
}

function done(idx: number) {
    tasks.value[idx].done = true;
}





</script>

<template>
    <h2>ToDo List</h2>
    <div>完了したタスク
        <ul>
            <li v-for="task in tasks" :key="task.name">
                <div v-if="task.done">{{ task.name }}</div>
            </li>
        </ul>
    </div>
    <div>未完了のタスク
        <ul>
            <li v-for="(task, idx) in tasks" :key="task.name">
                <div v-if="!task.done">
                    <button @click="done(idx)">おわった</button>
                    {{ task.name }}
                </div>
            </li>
        </ul>
    </div>
    <input v-model="newTaskName" type="text" />
    <button @click="addTask">タスクを追加</button>
</template>

<style></style>

