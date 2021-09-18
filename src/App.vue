<template>
  <div class="container">
    <!---->
    <Header
      v-on:toggle-add-task="toggleAddTask"
      title="Task Tracker"
      :showAddTask="showAddTask"
    />
    <div v-if="showAddTask">
      <AddTask v-on:add-task="addTask" />
    </div>
    <Tasks
      v-on:toggle-reminder="toggleReminder"
      v-on:delete-task="deleteTask"
      :tasks="tasks"
    />
    <router-view></router-view>
    <Footer/>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import Tasks from "./components/Tasks.vue";
import AddTask from "./components/AddTask.vue";
import Footer from "./components/Footer.vue";
export default {
  name: "App",
  components: {
    Header,
    Tasks,
    AddTask, 
    Footer,
  },
  data() {
    return {
      tasks: [],
      showAddTask: false, //to show/hide the addtask component
    };
  },
  //emits:['add-task','delete-task','toggle-reminder'],
  methods: {
    //on save task button
    async addTask(task) {
      const res = await fetch('http://localhost:5000/tasks', {
        method: 'POST',
        headers: {
          'Content-type': 'application/json',
        },
        body: JSON.stringify(task),
      })
      const data = await res.json();

      this.tasks = [...this.tasks, data];
    },
    //on clicking Addtask button
    toggleAddTask() {
      this.showAddTask = !this.showAddTask;
    },
    //on clicking the red delete button
   async deleteTask(id) {
      if (confirm("Are you sure?")) {
        const res= await fetch(`http://localhost:5000/tasks/${id}`,
        {
          method:'DELETE',
        }) 
        res.status==200 ?
        (this.tasks = this.tasks.filter((task) => task.id !== id)) : alert('Error deleting task')
      }
    },
    //for dblclicking (green color reminder)
    async toggleReminder(id) {
      const taskToToggle = await this.fetchTask(id)
      const updTask = {...taskToToggle,reminder:!taskToToggle.reminder}

    const res = await fetch(`http://localhost:5000/tasks/${id}`,{
      method:'PUT',
      headers:{
        'Content-type':'application/json'
      },
      body:JSON.stringify(updTask),

    })
    const data = await res.json
      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: data.reminder } : task
      )
    },
    async fetchTasks() {
      console.log("fetchTasks");
      const res = await fetch("http://localhost:5000/tasks");
      const data = await res.json();
      console.log(data);
      return data;
    },

    async fetchTask(id) {
      const res = await fetch("http://localhost:5000/tasks/" + id);
      const data = await res.json();
      return data;
    },

    async created() {
      console.log("created");
      this.tasks = await this.fetchTasks();
    },
  },
  created() {
    console.log("beforCreate hook has been called");
    this.tasks = this.created();
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: "Poppins", sans-serif;
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
