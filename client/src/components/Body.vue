<template>
  <div class="forBody" style="height:100%">
    <div class="container">
      <center>
        <form class="forTodo">
          <input type="text" class="inputTodo" v-model="newTodo" placeholder="Create to do.." style="height:50px;width:400px;border-radius:7px;font-family:Comic Sans MS">
          <button class="createTodo" v-on:click.prevent="submitTodo" style="height:47px;width:100px;border-radius:7px;background-color:pink;font-family:Comic Sans MS">Add</button>
        </form>
      </center>
      <br>
      <div class="row">
        <div class="col">
          <button class="titleTable" style="width:100%;background-color:green;height:50px;position:center;border-radius:5px;font-family:Comic Sans MS"> Back Log</button>
        </div>
        <div class="col">
          <button class="titleTable" style="width:100%;background-color:green;height:50px;position:center;border-radius:5px;font-family:Comic Sans MS"> To Do</button>
        </div>
        <div class="col">
          <button class="titleTable" style="width:100%;background-color:yellow;height:50px;position:center;border-radius:5px;font-family:Comic Sans MS"> Doing</button>
        </div>
        <div class="col">
          <button class="titleTable" style="width:100%;background-color:red;height:50px;position:center;border-radius:5px;font-family:Comic Sans MS"> Done</button>
        </div>
      </div>
      <div class="row">
        <div class="col">
            <div class="card" style="width: 250px;" v-for="todo in todos" v-bind:key="todo['.key']">
              <div class="card-body">
                <p class="card-title" style="font-family:Comic Sans MS"> {{todo.todo}}</p>
                <a href="#" @click="deleteTodo(todo.todo)" class="btn btn-primary mr-sm-2" style="font-family:Comic Sans MS">Delete <i class="fas fa-trash"></i></a>
                <a href="#" @click="addTodo(todo.todo)" class="btn btn-primary" style="font-family:Comic Sans MS">To Do <i class="fas fa-arrow-alt-circle-right"></i></a>
              </div>
            </div>
          </div>
          <div class="col">
            <div class="card" style="width: 250px;" v-for="todo in todoFields" v-bind:key="todo['.key']">
              <div class="card-body">
                <p class="card-title" style="font-family:Comic Sans MS">{{todo.todo}}</p>
                <a href="#" @click="backStart(todo.todo)" class="btn btn-primary mr-sm-2" style="font-family:Comic Sans MS"><i class="fas fa-arrow-alt-circle-left"></i> Back</a>
                <a href="#" @click="addToDoing(todo.todo)" class="btn btn-primary mr-sm-2" style="font-family:Comic Sans MS">Doing <i class="fas fa-arrow-alt-circle-right"></i></a>
              </div>
            </div>
          </div>
          <div class="col">
            <div class="card" style="width: 250px;" v-for="todo in doingTodos" v-bind:key="todo['.key']">
              <div class="card-body">
                <p class="card-title" style="font-family:Comic Sans MS">{{todo.todo}}</p>
                <a href="#" @click="backTodo(todo.todo)" class="btn btn-primary mr-sm-2" style="font-family:Comic Sans MS"><i class="fas fa-arrow-alt-circle-left"></i> Back</a>
                <a href="#" @click="addToDone(todo.todo)" class="btn btn-primary mr-sm-2" style="font-family:Comic Sans MS">Done <i class="fas fa-arrow-alt-circle-right"></i></a>
              </div>
            </div>
          </div>
          <div class="col">
            <div class="card" style="width: 250px;" v-for="todo in doneTodos" v-bind:key="todo['.key']">
              <div class="card-body">
                <p class="card-title" style="font-family:Comic Sans MS">{{todo.todo}}</p>
                <a href="#" @click="backToDoing(todo.todo)" class="btn btn-primary mr-sm-2" style="font-family:Comic Sans MS"><i class="fas fa-arrow-alt-circle-left"></i> Back</a>
              </div>
            </div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
import firebase from 'firebase'
var config = {
  apiKey: 'AIzaSyAM8euLgDrltPYvM5ZQrzWPy7XXsrdpVzA',
  authDomain: 'kanban-e0a32.firebaseapp.com',
  databaseURL: 'https://kanban-e0a32.firebaseio.com',
  storageBucket: ''
}
firebase.initializeApp(config)
let firebasetodos = firebase.database().ref('todos')
export default {
  name: 'forBody',
  props: [],
  data () {
    return {
      newTodo: '',
      todos: [],
      status: 'start',
      rawData: [],
      doingTodos: [],
      doneTodos: [],
      todoFields: []
    }
  },
  mounted () {
    this.gotData()
  },
  firebase () {
    return {
      gotodos: firebasetodos
    }
  },
  watch: {
    gotodos () {
      this.gotData()
    }
  },
  methods: {
    clear () {
      this.todos = []
      this.doingTodos = []
      this.doneTodos = []
      this.todoFields = []
    },
    submitTodo () {
      this.clear()
      firebase.database().ref('todos/' + this.newTodo).set({
        todo: this.newTodo,
        status: this.status
      })
      this.newTodo = ''
    },
    gotData () {
      var starCountRef = firebase.database().ref('todos/')
      starCountRef.on('value', snapshot => {
        var dataTodos = snapshot.val()
        var keys = Object.values(dataTodos)
        this.rawData = keys
        this.rawData.forEach(element => {
          if (element.status === 'start') {
            this.todos.push(element)
          } else if (element.status === 'todo') {
            this.todoFields.push(element)
          } else if (element.status === 'doing') {
            this.doingTodos.push(element)
          } else if (element.status === 'done') {
            this.doneTodos.push(element)
          }
        })
      })
    },
    deleteTodo (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set(null)
        }
      })
    },
    addTodo (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'todo' })
        }
      })
    },
    addToDoing (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'doing' })
        }
      })
    },
    backTodo (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'todo' })
        }
      })
    },
    backStart (key) {
      this.clear()
      this.gotodos.forEach(element => {
        console.log(element.todo)
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'start' })
        }
      })
    },
    addToDone (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'done' })
        }
      })
    },
    backToDoing (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'doing' })
        }
      })
    }
  }
}
</script>
