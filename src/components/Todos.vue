<template>
  <section class="todoapp">
   
    <header class="header">
      <h1> Todos </h1>
    </header>
    
    <input type="text" class="new-todo" placeholder="Add a task" v-model="newtodo" @keyup.enter="addTodo">

      <div class="main">
        <input type="checkbox" class="toggle-all" v-model="allDone">
        <ul class="todo-list">
          <li class="todo" v-for="todo in filteredTodos" :class="{completed: todo.completed, editing: todo === editing}">
            <div class="view">
              <input type="checkbox" class="toggle" v-model="todo.completed">
              <label @dblclick="editTodo(todo)" > {{todo.name}} </label>
              <button class="destroy" @click.prevent="deleteTodo(todo)" ></button>
            </div>
            <input type="text" class="edit" v-model="todo.name" @keyup.enter="doneEdit()" @blur="doneEdit()" @keyup.esc="cancelEdit()" v-focus="todo === editing">
          </li>
        </ul>
      </div>

    <footer class="footer" v-show="hasTodos">
      <span class="todo-count"><strong>{{remaining}}</strong> Pendings</span>
      <ul class="filters">
        <li><a href="" :class="{selected : filter === 'all' }"  @click.prevent="filter = 'all' " > All </a></li>
        <li><a href="" :class="{selected : filter === 'todo' }" @click.prevent="filter = 'todo' "> Pendings </a></li>
        <li><a href="" :class="{selected : filter === 'done' }" @click.prevent="filter = 'done' "> Done </a></li>
      </ul>
      <button class="clear-completed" v-show="completed" @click.prevent="deleteCompleteted()">Delete terminated tasks</button>
    </footer>
 
  </section>
</template>

<script> 

import Vue from 'vue';

export default {
  name: 'todos',
  data () {
    return {
     todos : [],
     newtodo : '',
     filter : 'all',
     editing : null,
     oldTodo : null,
    }
  },
  methods:{

    addTodo(){
      
      this.todos.push({
        completed : false,
        name: this.newtodo
      });

      this.newtodo = "";

    },
    deleteTodo(todo){

      this.todos = this.todos.filter( t => t !== todo );

    },

    deleteCompleteted(){

      this.todos = this.todos.filter( t => ! t.completed );

    },

    editTodo(todo){

      this.editing = todo;
      this.oldTodo = todo.name;

      

    },

    doneEdit(){

      this.editing = null; 

    },

    cancelEdit(){

      this.editing.name = this.oldTodo;
      
      this.doneEdit();


    },

  },
  computed : {

    allDone: {

      get(){
        return this.remaining === 0;
      },

      set(value){
         this.todos.forEach( t => t.completed = value );
      }

    },

    remaining() {
      return this.todos.filter( t => !t.completed).length;
    },

    completed(){
      return this.todos.filter( t => t.completed);
    },

    filteredTodos(){

      if( this.filter === "all") return this.todos;

      if( this.filter === "todo") return this.todos.filter( t => !t.completed);

      if( this.filter === "done") return this.todos.filter( t => t.completed);
      
    },
    hasTodos(){
      return this.todos.length > 0 ? true : false;
    }
  },
  directives:{

    focus (el,value){

      if(value) Vue.nextTick( _ => el.focus() ); // nextTick == Next Dom refresh ( hel t fix bug )

    }

  },
  created(){

      let retrievedObject = localStorage.getItem('todos');

      if( retrievedObject ) this.todos = JSON.parse(retrievedObject);

  },
  watch: {

    todos: {
      handler(val){
       
        localStorage.setItem( 'todos', JSON.stringify(this.todos) );

      },
     deep: true // to watch nested element of the object

  }

  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
@import './todo.css';

</style>
