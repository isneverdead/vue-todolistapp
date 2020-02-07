<template>
  <div>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.2/animate.min.css">
    <div>Ransel ku</div>
    <input type="text" class="todo-input" placeholder="Barang"
    v-model="newTodo" @keyup.enter="addTodo">
    <transition-group name="fade" enter-active-class="animated fadeInUp faster" leave-active-class="animated hinge fast">
    <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
        <div class="todo-item-left">
            
            <input type="checkbox" v-model="todo.completed">
            
            <div v-if="!todo.editing" @dblclick="editTodo(todo)" 
            class="todo-item-label" :class="{ completed : todo.completed }">{{ todo.title }}</div>
            
            <input v-else class="todo-item-edit" type="text" 
            v-model="todo.title" @blur="doneEdit(todo)" 
            @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)"
            v-focus>
            
        </div>
        <div class="remove-item" @click="removeTodo(index)">
            &times;
        </div>
    </div>
    </transition-group>
    <div class="extra-container">
        <div><label><input type="checkbox" :checked="anyRemaining" 
        @change="checkAllTodos"> Check All </label></div>
        <div>{{ remaining }} item left</div>
    </div>

    <div class="extra-container">
        <div>
            <button :class="{ active: filter == 'all' }" @click="filter = 'all'">All</button>
            <button :class="{ active: filter == 'active' }" @click="filter = 'active'">Active</button>
            <button :class="{ active: filter == 'completed' }" @click="filter = 'completed'">Completed</button>
        </div>

        <div> 
            <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
        </div>

    </div>
  </div>
</template>

<script>
export default {
  name: 'TodoList',
  data () {
    return {
        newTodo: '',
        idForTodo: 4,
        beforeEditCache: '',
        filter: 'all',
        todos: [
            {
                'id': 1,
                'title': 'Laptop',
                'completed': false,
                'editing': false,
            },
            {
                'id': 2,
                'title': 'Charger',
                'completed': false,
                'editing': false,
            },
            {
                'id': 3,
                'title': 'Alat tulis',
                'completed': false,
                'editing': false,
            },
        ]
    }
    },
    directives: {
        focus: {
            inserted: function (el) {
                el.focus()
            }
        }
    },
    computed: {
        remaining() {
            return this.todos.filter(todo => !todo.completed).length
        },
        anyRemaining() {
            return this.remaining == 0
        },
        todosFiltered() {
            if (this.filter == 'all') {
                return this.todos
            } else if (this.filter == 'active') {
                return this.todos.filter(todo => !todo.completed)
            } else if (this.filter == 'completed') {
                return this.todos.filter(todo => todo.completed)
            }
        },
        showClearCompletedButton() {
            return this.todos.filter(todo => todo.completed).length > 0
        }
    },
    methods: {
        addTodo() {
            if (this.newTodo.trim().length == 0) {
                return
            }
            this.todos.push({
                id: this.idForTodo,
                title: this.newTodo,
                completed: false,
            })

            this.newTodo = ''
            this.idForTodo++
        },
        editTodo(todo) {
            this.beforeEditCache = todo.title
            todo.editing = true
        },
        doneEdit(todo) {
            if (this.title.trim().length == 0) {
                todo.title = this.beforeEditCache
            }
            todo.editing = false
        },
        cancelEdit(todo) {
            todo.title = this.beforeEditCache
            todo.editing = false
        },
        removeTodo(index) {
            this.todos.splice(index, 1)
        },
        checkAllTodos() {
            this.todos.forEach((todo) => todo.completed = event.target.checked)
        },
        clearCompleted() {
            this.todos = this.todos.filter(todo => !todo.completed  )
        }
    },
        
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
    .todo-input {
        width: 100%;
        padding: 10px 18px;
        font-size: 18px;
        margin-bottom: 16px;
    }
    .todo-item {
        margin-bottom: 12px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        animation-duration: 1s;
    }
    .remove-item {
        cursor: pointer;
        margin-left: 14px;
    }
    .todo-item-left {
        display: flex;
        align-items: center;
    }
    .todo-item-label {
        padding: 10px;
        border: 1px solid white;
        margin-left: 12px;
    }
    .todo-item-edit {
        font-size: 24px;
        color: #2c3e50;
        margin-left: 12px;
        width: 100%;
        padding: 10px;
        border: 1px solid #ccc;
        font-family: 'Avenir', Arial, Helvetica, sans-serif;
    }
    .completed {
        text-decoration: line-through;
        color: gray;
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
    }
    
    .active {
        background: lightgreen;
    }

</style>
