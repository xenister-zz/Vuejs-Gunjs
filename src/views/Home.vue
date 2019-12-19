<template>
  <div id="app">
    <AddTodo v-on:add-todo="addTodo"/>
    <Todos v-bind:todos="todos" v-on:del-todo="deleteTodo" v-on:mark-completed="markCompleted"/>
  </div>
</template>

<script>

import Todos from '../components/Todos.vue'
import Header from '../components/layout/Header.vue'
import AddTodo from '../components/AddTodo.vue'
import uuid from 'uuid'
import axios from 'axios'
import Gun from 'gun'
import unset from 'gun/lib/unset'
import path from 'gun/lib/path'
import open from 'gun/lib/open'
import not from 'gun/lib/not'

var gun = Gun()
var gunWeb = gun.get('WebDB').get('Todos')

// src="https://cdn.jsdelivr.net/npm/gun/lib/unset.js"

export default {
  name: 'Home',
  components: {
    Todos,
    AddTodo
  },
  data() {
    return {
      todos: []
    }
  },

  // gunWeb.get(this.todo.id).get('completed').put(this.todo.completed)
  methods: {

    markCompleted(idCompleted) {
      console.log("MARKCOMPLETED---*-*-*-*-", this.todos)

      // let temp;
      // for (let i in this.todos) {
      //   console.log("IN LOOP == ",this.todos[i])
      //   if (this.todos[i].id == idCompleted) {
      //     this.todos[i].completed = !this.todos[i].completed 
      //     temp = this.todos[i].completed
      //     break;
      //   }
      // }
      // console.log("MARKCOM ==== ", temp)

      gunWeb.get(idCompleted).path('completed').put(true)

      setTimeout( ()=> {
        console.log("MARKCOMPLETED---*-*-*-*-", this.todos)
      }, 3000)

      console.log("MARKCOMPLETED---*-*-*-*-", this.todos)
    },

    deleteTodo(id) {
      
      gunWeb.get(id).put(null)
      this.todos = this.todos.filter( todo => {
          return todo.id !== id
      })
    },

    addTodo(newTodo) {
      console.log("Add TODO")

      gunWeb.get(newTodo.id).put(newTodo)
    }
  },


  created() {
    console.log('IN CREATED GUN')

    var defaultTodo = {
      id: uuid.v4(),
      title: "This is a default Todos",
      completed: false,
    }

    gunWeb.not( () => {
      // var defaultCreate = gun.get(idTemp).put(defaultTodo)
      gunWeb.get(defaultTodo.id).put(defaultTodo)
    })

    gunWeb.map((elem)=> {
      console.log("ELEM == ", elem)
      if (elem !== null) {
        this.todos = [...this.todos, elem]
        // console.log("Todos Content == ", this.todos)
      }
      
      // console.log("Todos OUUUUT ", this.todos)
    })
    this.todos = temp

    // setTimeout( ()=> {
    //     gunWeb.get('866038c5-1778-43ed-b4ec-2ff6dbbc659b').put(null)
    // }, 3000)
  },

  // created() {
  //     axios.get('https://jsonplaceholder.typicode.com/todos?_limit=7')
  //     .then(res => this.todos = res.data)
  //     .catch(err => console.log(err));
  // }


}
</script>

<style>
  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }
  body {
    font-family: Arial, Helvetica, sans-serif;
    line-height: 1.4;
  }
  .btn {
    display: inline-block;
    border: none;
    background: #555;
    color: #fff;
    padding: 7px 20px;
    cursor: pointer;
  }
  .btn:hover {
    background: #666;
  }
</style>
