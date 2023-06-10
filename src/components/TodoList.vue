<script setup lang="ts">
import { computed, reactive } from 'vue';
import { ref } from 'vue'

const idx = ref(0)

if (localStorage.getItem('ls')) {
    idx.value = JSON.parse(localStorage.getItem('ls')).length
}

interface Task {
    index: number,
    name: string,
    done: boolean,
}

const tasks = ref<Task[]>([
    // { index: 0, name: 'ToDoリストをつくる', done: true }
])

if (localStorage.getItem('ls')) {
    tasks.value = JSON.parse(localStorage.getItem('ls'))
}

let taskNameSet = new Set<string>();
taskNameSet = reactive(taskNameSet)
// taskNameSet.add(tasks.value[0].name)

const newTaskName = ref('')

const hasSameTask = ref<boolean>(false)
const addTask = () => {
    // console.log(taskNameSet)
    if (taskNameSet.has(newTaskName.value)) {
        hasSameTask.value = true
    } else if (newTaskName.value != '') {
        hasSameTask.value = false
        tasks.value.push({ index: idx.value, name: newTaskName.value, done: false })
        taskNameSet.add(newTaskName.value)
        newTaskName.value = ''
        idx.value++
    }
    localStorage.setItem('ls', JSON.stringify(tasks.value))
}

function getDone(idx: number) {
    tasks.value[idx].done = true;
    localStorage.setItem('ls', JSON.stringify(tasks.value))
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
                <button @click="getDone(task.index)">おわった</button>
                {{ task.name }}
            </li>
        </ul>
    </div>
    <input v-model="newTaskName" type="text" />
    <button @click="addTask">タスクを追加</button>
    <div v-if="hasSameTask" class="err">すでに同名のタスクがあります！</div>
</template>

<style>
.done {
    color: gray;
}

.err {
    color: red;
}
</style>

