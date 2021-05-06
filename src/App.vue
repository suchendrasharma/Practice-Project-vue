<template>
<div class="container">
  <Header @toggle-add-task="toggleAddTask" title="Task Tracker" :showAddTask="showAddTask" />
  <div v-show="showAddTask">
    <AddTask @add-task='addTask' />
  </div>
  
  <Tasks @toogle-reminder="toggleReminder" @delete-task="deleteTask" :tasks="tasks" />
  <!-- <router-view></router-view> -->
  <Footer />
</div>

  
</template>

<script>
import Header from './components/Header';
import Footer from './components/Footer';
import Tasks from './components/Tasks';
import AddTask from './components/AddTask';

export default {
  name: 'App',
  components: {
    Header,
    Footer,
    Tasks,
    AddTask
  },
  data() {
    return {
    tasks: [],
    showAddTask: false,
    }
  },
  methods: {
    toggleAddTask(){
      this.showAddTask = !this.showAddTask
    },
    async addTask(task) {

      const res = await fetch('api/tasks', {
        method: 'POST',
        headers: {
          'Content-type':  'application/json',
        },
        body: JSON.stringify(task)
      })

      const data = await res.json()


      this.tasks = [...this.tasks, data]
    },
    async deleteTask(id) {
      if(confirm('Are u sure?')){

        const res = await fetch(`api/tasks/${id}`, {
          method: 'DELETE',
        })

        res.status === 200 ? (this.tasks = this.tasks.filter((task) => task.id !== id)) : alert('Error deleting task')        
      }
    },
    async toggleReminder(id){

      const taskToToggle = await this.fetchTask(id)
      const updTask = {...taskToToggle, reminder: !taskToToggle.reminder}

      const res = await fetch(`api/tasks/${id}`, {
        method: 'PUT',
        headers: {
          'Content-type': 'application/json'
        },
        body: JSON.stringify(updTask)
      })

      const data = await res.json()

      this.tasks = this.tasks.map((task) => task.id === id ? {
        ...task, reminder: data.reminder
      }: task
      )
    },
    async fetchTasks() {
      const res = await fetch('api/tasks')

      const data = await res.json()

      return data
    },
    async fetchTask(id) {
      const res = await fetch(`api/tasks/${id}`)

      const data = await res.json()

      return data
    }
  },
  async created() {
    this.tasks = await  this.fetchTasks()
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');

*{
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
</style>
