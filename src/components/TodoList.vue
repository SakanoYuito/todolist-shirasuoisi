<script setup lang="ts">
import { computed, reactive } from 'vue';
import { ref } from 'vue'

const DAYS = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]
const CUMDAYS = [0, 31, 59, 90, 120, 151, 181, 212, 243, 273, 304, 334]
const INF = 10000

const t = new Date()
const today = CUMDAYS[t.getMonth()] + t.getDate()


const idx = ref(0)

if (localStorage.getItem('ls')) {
    idx.value = JSON.parse(localStorage.getItem('ls') || '{}').length
}

interface Task {
    index: number,
    name: string,
    done: boolean,
    due: string,
}

const tasks = ref<Task[]>([
    // { index: 0, name: 'ToDoリストをつくる', done: true }
])
let taskNameSet = new Set<string>();
taskNameSet = reactive(taskNameSet)

if (localStorage.getItem('ls')) {
    tasks.value = JSON.parse(localStorage.getItem('ls') || '{}')
}
for(let elem of tasks.value){
    taskNameSet.add(elem.name)
}

const newTaskName = ref('')
const hasSameTask = ref(false)
const newTaskDue = reactive<[string, string]>(['', ''])



const addTask = () => {
    // console.log(taskNameSet)
    if (taskNameSet.has(newTaskName.value)) {
        hasSameTask.value = true
    } else if (newTaskName.value != '') {
        hasSameTask.value = false
        tasks.value.push({ index: idx.value, name: newTaskName.value, done: false, due: dueFormatter() })
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

function dueFormatter(): string {
    if (newTaskDue[0] == '' || newTaskDue[1] == '') { return "期限なし" }
    else { return `${newTaskDue[0]}月${newTaskDue[1]}日` }
}

function dueDateCalc(due: string): number {
    if(due == '期限なし') {return INF}
    const dues = due.slice(0, -1).split("月")

    return CUMDAYS[Number(dues[0])] + Number(dues[1])
}

const doneTasks = computed(() => tasks.value.filter((task) => task.done))
const undoneTasks = computed(() => tasks.value.filter((task) => !task.done))


</script>

<template>
    <h2>ToDo List</h2>
    <div>
        <h3>完了したタスク</h3>
        <ul>
            <li v-for="task in doneTasks" :key="task.name" class="done">
                <div>{{ task.name }}</div>
            </li>
        </ul>
    </div>
    <div>
        <h3>未完了のタスク</h3>
        <ul>
            <div v-if="undoneTasks.length == 0">
                未完了のタスクはありません。全部終わってえらいね
            </div>
            <li v-for="task in undoneTasks" :key="task.name" :class="{ delayed : today >= dueDateCalc(task.due)}">
                <button @click="getDone(task.index)">おわった</button>
                {{ task.name }}
                {{ task.due }}
            </li>
        </ul>
    </div>
    <div>
        <h3>新規タスクの追加</h3>
        <div>名前：
            <input v-model="newTaskName" type="text" />
        </div>
        <div>期限：
            <select v-model="newTaskDue[0]" id="formMonth">
                <option value="" selected>--</option>
                <option v-for="m in 12" :key="m" v-bind:value="m">{{ m }}</option>
            </select>
            月 /
            <select v-model="newTaskDue[1]" id="formDay">
                <option value="" selected>--</option>
                <option v-for="d in DAYS[Number(newTaskDue[0]) - 1]" :key="d" v-bind:value="d">{{ d }}</option>
            </select>
            日
        </div>
        <button @click="addTask">追加</button>
        <div v-if="hasSameTask" class="err">すでに同名のタスクがあります！</div>
    </div>
</template>

<style>
.done {
    color: gray;
}

.err, .delayed{
    color: red;
}
</style>

