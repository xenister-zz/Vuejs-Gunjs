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

  methods: {

    markCompleted(idCompleted, isCompleted) {
      gunWeb.get(idCompleted).path('completed').put(isCompleted)
      gunWeb.open(console.log)
    },

    deleteTodo(id) {
      gunWeb.get(id).put(null)
      this.todos = this.todos.filter( todo => {
          return todo.id !== id
      })
    },

    addTodo(newTodo) {
      gunWeb.get(newTodo.id).put(newTodo)
    }
  },

  created() {

    var defaultTodo = {
      id: uuid.v4(),
      title: "This is a default Todos",
      completed: false,
    }

    gunWeb.not( () => {
      gunWeb.get(defaultTodo.id).put(defaultTodo)
    })

    gunWeb.map().val( (elem)=> {
      if (elem !== null) {
        this.todos = [...this.todos, elem]
      }
    })
  },

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
