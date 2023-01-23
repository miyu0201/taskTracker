<template>
<div>
    <div :key ="task.id" v-for="task in tasks">
         <!-- receive the emited function detele-task from Task, then crerate a new emit to upper level App -->
     <Task @toggle-reminder="$emit('toggle-reminder', task.id)" @delete-task="$emit('delete_task',task.id)" :task="task"/>
     <!-- <h3>{{task.text}}</h3> -->
    </div>
 </div>
</template>

<script>
import Task from "./Task"

export default {
    name:'Tasks',
    props:{
        tasks: Array
    },
    components:{
        Task,
    },
    //emits:['delete-task','toggle-reminder']
   
}
</script>