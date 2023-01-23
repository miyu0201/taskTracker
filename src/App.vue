<template>
<div class="container">
    <Header @toggle-add-task="toggleAddTask" 
       :showAddTask="showAddTask" 
       title="Task Tracker" />
    <div v-if="showAddTask"><!--or use v-show:"showAddTask" -->
    <AddTask @add-task="addTask"/>
    </div>
    
    <Tasks @toggle-reminder="toggleReminder" @delete_task="deleteTask" :tasks='tasks'/>
    <Footer/>
</div>
</template>

<script>
import Header from "./components/Header";
import Tasks from "./components/Tasks";
import AddTask from "./components/AddTask";
import Footer from "./components/Footer";

export default {
  name: "App",
  components: {
    Header,
    Tasks,
    AddTask,
    Footer
  },
 data() {
  return {
    tasks: [],
    showAddTask:false
  }
  },

  methods:{

    toggleAddTask(){
       this.showAddTask=!this.showAddTask
    },

     /*with no server
     addTask(task){

      this.tasks=[...this.tasks,task]
     }*/

    //with server 
    async addTask(task){
      const res = await fetch('http://localhost:5000/tasks', {
        method:'POST',
        headers:{
          'Content-type':'application/json',         
        },
        body:JSON.stringify(task),
      })

      const data = await res.json()

      this.tasks = [...this.tasks, data]
    },
    /*with no server
    deleteTask(id){
      console.log(id+"deleted")
      if(confirm('Are you sure?')){
       //filter out the one that id==the id passed in
      this.tasks=this.tasks.filter((task)=>task.id!==id)
      }
      
    },*/
    //with server
    async deleteTask(id){
      if (confirm('Are you sure')){
        //use back tick `` to include $
        const res = await fetch(`http://localhost:5000/tasks/${id}`, {
          method:'DELETE',
        })
        //delete directly 
        //this.tasks = this.tasks.filter((task)=>task.id!==id)

        // or check if DELETE request is received by server, then delete task
      //The HTTP 200 OK success status response code indicates that the request has succeeded
      res.status===200
      ? (this.tasks = this.tasks.filter((task)=>task.id!==id))
      : alert('Error deleting task!')
      }
    },
    /*with no server 
    toggleReminder(id){
      this.tasks=this.tasks.map((task)=> task.id===id ? 
        {...task, reminder: !task.reminder } : task)
      
    },*/

  //with server
   async toggleReminder(id){
    //fetch the single task that need to update
    const taskToToggle = await this.fetchTask(id)
    //toggle the reminder status
    const updTask = {...taskToToggle, reminder:!taskToToggle.reminder}
    
    //make put request to server
    const res = await fetch(`http://localhost:5000/tasks/${id}`,{
      method:'PUT', //for update
      headers:{
        'Content-type':'application/json'
      },
      body:JSON.stringify(updTask)
    })

  //update task with new data: updTask reminder
    const data = await res.json()
    this.tasks=this.tasks.map((task)=> task.id===id ? 
        {...task, reminder: data.reminder } : task)
    },

    async fetchTasks(){
      const res = await fetch('http://localhost:5000/tasks')
     //or if add proxy in vue.config, can use const res = await fetch('api/tasks')
     //need to restart serve
      const data = await res.json()

      return data
    },

    //fetch single task, for toggle reminder
    async fetchTask(id){
      const res = await fetch(`http://localhost:5000/tasks/${id}`)
     //or if add proxy in vue.config, can use const res = await fetch('api/tasks')
     //need to restart serve
      const data = await res.json()

      return data
    },

  },

//when app runs ,it will run created and assign the value for the array tasks
  async created(){
   this.tasks=await this.fetchTasks()
    /*
     this.tasks=[
      {
        id:1,
        text:'weekly meeting',
        day:'2012-2-05',
        reminder:false
      },
      {
        id:2,
        text:'doctor appointment',
        day:'2013-6-11',
        reminder:true
      },
     
     {
        id:3,
        text:'interview',
        day:'2013-9-18',
        reminder:false
      },
     
     {
        id:4,
        text:'conference',
        day:'2014-12-25',
        reminder:true
      }
     
     ]*/
  }
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: 'Poppins', sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}
.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}
.btn:focus {
  outline: none;
}
.btn:active {
  transform: scale(0.98);
}
.btn-block {
  display: block;
  width: 100%;
}
</style>
