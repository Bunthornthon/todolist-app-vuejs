<template>
  <div class="fixed bg-white w-screen md:px-60 px-10">
    <h1 class="text-white text-3xl font-bold py-4 my-3 w-60 bg-green-600 rounded mx-auto text-center">My
      Todo
      App
    </h1>
    <!-- input -->
    <div class="flex justify-center">
      <input v-model.trim="task" type="text" placeholder="Enter task" class="w-full rounded" @keyup.enter="submitTask">
      <button v-show="!search" class="rounded bg-green-500 text-white hover:bg-slate-400 mx-1 px-3 text-xl"
        @click="submitTask">
        <i class="fa-solid fa-file-circle-plus"></i>
      </button>
      <button v-show="!search" class="rounded bg-orange-400 hover:bg-slate-400 px-3 text-xl text-white"
        @click="searchTodo()">
        <i class="fa-solid fa-magnifying-glass"></i>
      </button>
      <button v-show="search" @click="callBackData"
        class="rounded bg-green-500 text-white hover:bg-slate-400 mx-1 px-3 text-xl">
        <i class="fa-solid fa-rectangle-xmark"></i>
      </button>
      <button v-show="search" @click="submitTask"
        class="rounded bg-orange-400 hover:bg-slate-400 px-3 text-xl text-white">
        <i class="fa-solid fa-download"></i>
      </button>
    </div>
  </div>
  <div class="mx-auto px-10 md:px-60 py-24">
    <!-- table task -->
    <div class="mx-auto pt-10">
      <table class="mx-auto w-full">
        <thead class="border-b bg-slate-300 border-green-700">
          <tr class="">
            <th scope="col" class="text-sm font-bold  text-gray-900 p-4 text-center">Task</th>
            <th scope="col" class="text-sm font-bold w-[80px]  text-gray-900 text-center ">Status</th>
            <th scope="col" class="text-sm font-bold  text-gray-900  text-center px-1">Edit</th>
            <th scope="col" class="text-sm font-bold  text-gray-900 text-center pr-2">Delete</th>
          </tr>
        </thead>
        <tbody class="bg-slate-300">
          <tr class="bg-gray-100 border-b border-emerald-700" v-for="(task, index) in tasks" :key="index">
            <td class="py-2 whitespace-nowrap text-sm font-medium text-gray-900 text-left ">
              <span :class="{ 'line-through decoration-2 decoration-orange-800': task.status === 'complete' }">
                {{ task.title }}
              </span>
            </td>
            <td class="text-sm text-gray-900 font-bold whitespace-nowrap text-center cursor-pointer ">
              <span @click="changeStatus(index)"
                :class="{ 'text-green-500': task.status === 'to-do', 'text-yellow-400': task.status === 'in-progress', 'text-red-600': task.status === 'complete' }">
                {{ task.status }}
              </span>
            </td>
            <td class="text-sm text-gray-900 font-light whitespace-nowrap text-center">
              <div class=" px-1" @click="editTask(index)">
                <i class="fa-solid fa-pen-to-square cursor-pointer text-yellow-400 hover:text-yellow-600 text-xl"></i>
              </div>
            </td>
            <td class="text-sm text-gray-900 font-light whitespace-nowrap text-center">
              <div class=" px-1" @click="deleteTask(index)">
                <i class="fa-solid fa-trash-can cursor-pointer text-red-600 hover:text-red-900 text-xl"></i>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
<script>
export default {
  name: 'TodoApp',
  components: {},
  props: {
    msg: String,
  },
  data() {
    return {
      id: 0,
      index: 0,
      task: '',
      editedTask: null,
      availableStatuses: ['to-do', 'in-progress', 'complete'],
      tasks: [],
      search: false
    }
  },
  mounted() {
    this.tasks = JSON.parse(localStorage.getItem('tasks')) || [];
    if (this.tasks.length == 0) {
      fetch('https://dummyjson.com/posts')
        .then(res => res.json())
        .then(data => {
          this.tasks = data.posts;
          this.tasks.forEach(element => {
            element.status = "to-do"
            localStorage.setItem('tasks', JSON.stringify(this.tasks))
          });
          //Set localstorage
        });
    }
  },
  methods: {
    callBackData() {
      // location.reload()
      this.tasks = JSON.parse(localStorage.getItem('tasks'))
      console.log(this.callBackData)
      this.search = !this.search

    },
    saveTask() {
      //Set localstorage
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
      console.log(this.saveTask)
    },
    searchTodo() {
      this.search = !this.search
      this.tasks = JSON.parse(localStorage.getItem('tasks'))
      this.tasks = this.tasks.filter((tasks) =>
        tasks.title.toLowerCase().includes(this.task.toLowerCase()),
        console.log(this.searchTodo)
      );
      this.task = ''
    },
    submitTask() {
      if (this.task.length === 0) return;
      if (this.editedTask === null) {
        // have  edit data 
        this.index = JSON.parse(localStorage.getItem('tasks')) || []
        this.index = this.tasks.length + 1
        console.log(this.index)
        this.tasks.unshift({
          id: this.index,
          title: this.task,
          status: 'to-do',
        })
        console.log(this.submitTask)
        //Set localstorage
        localStorage.setItem('tasks', JSON.stringify(this.tasks));
      } else {
        // have no edit data 
        this.tasks = JSON.parse(localStorage.getItem('tasks'))
        this.tasks[this.editedTask].title = this.task;
        this.editedTask = null;
        this.search = !this.search
        //Set localstorage
        localStorage.setItem('tasks', JSON.stringify(this.tasks));
      }
      this.task = ''
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
      console.log(this.deleteTask)
    },
    editTask(index) {
      this.task = this.tasks[index].title;
      this.editedTask = index;
      console.log(this.editTask)
    },
    changeStatus(index) {
      let newIdex = this.availableStatuses.indexOf(this.tasks[index].status)
      if (++newIdex > 2) newIdex = 0
      this.tasks[index].status = this.availableStatuses[newIdex]
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },
    capitalizeFirstChar(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
  }
}
</script>
