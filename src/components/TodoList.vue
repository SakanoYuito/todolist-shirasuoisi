<script setup lang="ts">
// import { computed } from '@vue/reactivity';
import { computed } from '@vue/reactivity';
import { ref } from 'vue'

const idx = ref(0)

interface Task {
    index: number,
    name: string,
    done: boolean,
}

const tasks = ref<Task[]>([
    { index: idx.value, name: 'ToDoリストをつくる', done: true }
])

const newTaskName = ref('')

const addTask = () => {
    if (newTaskName.value != '') {
        tasks.value.push({ index: idx.value, name: newTaskName.value, done: false })
        newTaskName.value = ''
    }
    idx.value++
}

function getDone(idx: number) {
    tasks.value[idx].done = true;
}


const doneTasks = computed(() => tasks.value.filter((task) => task.done))
const undoneTasks = computed(() => tasks.value.filter((task) => !task.done))


</script>

<template>
    <h2>ToDo List</h2>
    <div>完了したタスク
        <ul>
            <li v-for="task in doneTasks" :key="task.name" class="done">
                <div>{{ task.name }}</div>
            </li>
        </ul>
    </div>
    <div>未完了のタスク
        <ul>
            <div v-if="undoneTasks.length == 0">
                未完了のタスクはありません。全部終わってえらいね
            </div>
            <li v-for="task in undoneTasks" :key="task.name">
                <button @click="getDone(idx)">おわった</button>
                {{ task.name }}
            </li>
        </ul>
    </div>
    <input v-model="newTaskName" type="text" />
    <button @click="addTask">タスクを追加</button>
</template>

<style>
.done {
    color: gray;
}
</style>

