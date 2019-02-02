<template>
  <div>
      <input type="text" placeholder="What need to be add" class="todo-input" v-model="newTodo" @keyup.enter="addTodo">
      <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
          <div class="todo-item-left">
            <input type="checkbox" v-model="todo.completed">
            <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label" :class="{ completed: todo.completed }">{{ todo.title }}</div>
            <input v-else class="todo-item-edit" type="text" v-model="todo.title" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" v-focus @keyup.esc="cancelTodo(todo)">
          </div>
          <div class="remove-item" @click="removeTodo(index)">
              &times;
          </div>
      </div>
      <div class="extra-container">
          <div><label><input type="checkbox" :checked="!anyRemaining" @change="checkForAllTodo">Check All</label></div>
          <div>{{ remaining }} item left</div>
      </div>
      <div class="extra-container">
          <div>
              <button :class="{ active: filter=='all' }" @click="filter = 'all'">All</button>
              <button :class="{ active: filter=='active' }" @click="filter = 'active'">Active</button>
              <button :class="{ active: filter=='completed' }" @click="filter = 'completed'">Completed</button>
          </div>
      </div>
  </div>
</template>

<script>
export default {
  name: 'todo-list',
  data () {
    return {
      newTodo: '',
      idForTodo: 4,
      beforeEditCache: '',
      filter: 'all',
      todos: [
          {
              id: 1,
              title: 'Fist task',
              completed: false,
              editing: false
          },
          {
              id: 2,
              title: 'Second task',
              completed: true,
              editing: false
          },
          {
              id: 3,
              title: 'Third task',
              completed: true,
              editing: false
          }
      ]
    }
  },
  computed: {
      remaining() {
          return this.todos.filter(todo => !todo.completed).length
      },
      anyRemaining() {
          return this.remaining != 0
      },
      todosFiltered() {
          if(this.filter == 'all') return this.todos;
          else if(this.filter == 'active') return this.todos.filter(todo => !todo.completed)
          else if(this.filter == 'completed') return this.todos.filter(todo => todo.completed)

          return this.todos
      }
  },
  directives:{
      focus: {
          inserted: function(el){
              el.focus()
          }
      }
  },
  methods: {
      addTodo() {
          if(!this.newTodo.trim()) return

          this.todos.push({
              id: this.idForTodo,
              title: this.newTodo,
              completed: false,
              editing: false
          })

          this.newTodo = ''
          this.idForTodo++
      },
      removeTodo(index) {
          this.todos.splice(index, 1)
      },
      editTodo(todo) {
          todo.editing = true
          this.beforeEditCache = todo.title
      },
      doneEdit(todo) {
          if(!todo.title.trim()) todo.title = this.beforeEditCache
          todo.editing = false
      },
      cancelTodo(todo) {
          todo.title = this.beforeEditCache
          todo.editing = false
      },
      checkForAllTodo() {
          this.todos.forEach((todo) => todo.completed = event.target.checked)
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
.todo-input{
    width: 100%;
    padding: 10px 10px;
    font-size: 18px;
    margin-bottom: 16px;

    &:focus{
        outline: 0;
    }
}

.todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.remove-item {
    cursor: pointer;
    margin-left: 14px;
    &:hover{
        color: black;
    }
}

.todo-item-left {
    display: flex;
    align-items: center;
}

.todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 10px;
}

.todo-item-edit {
    font-size: 20px;
    color: brown;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, sans-serif;

    &:focus {
        outline: none;
    }
}

.completed {
    text-decoration: line-through;
    color: grey;
}

.extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgray;
    padding-top: 14px;
    margin-bottom: 14px;
}

button {
    font-size: 14px;
    background-color: white;
    appearance: none;

    &:hover {
        background: lightgreen;
    }

    &:focus {
        outline: none;
    }
}

.active {
    background: lightgreen;
}
</style>
