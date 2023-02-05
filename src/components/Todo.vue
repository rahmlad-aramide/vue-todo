<template>
    <div class="body" :class="{'dark':isdarkMode}">
        <div class="header-img transition"></div>
        <div class="main">
            <div class="head">
                <h1 class="title">{{title}}</h1>
                <transition-group name="button" tag="button" class="pointer" @click="toggle(!isdarkMode)">
                    <img :key="1" v-if="!isdarkMode" src="../assets/images/icon-moon.svg" alt="theme" />
                    <img :key="2" v-else src="../assets/images/icon-sun.svg" alt="theme" />
                </transition-group>
            </div>
            <form class="input-div transition">
                <button :key="3" type="submit"  @click="addTodo" :disabled="!item"></button>
                <input :key="4" type="text" v-model="item" placeholder="Create a new todo..." />
            </form> 
            <div class="">
            <div class="todo-body transition">
                <transition-group v-if="isFiltered" name="list" tag="ul" class="todo-list"> 
                    <li v-for="(todo, i) in filteredTodos" :key="i" class="list-item">
                        <div class="todo-div">
                            <span class="todo">
                                <button class="btn-complete" @click="completeFilter(todo)"> 
                                    <img src='../assets/images/icon-check.svg' :class="{'none': todo.isCompleted}" />
                                </button>
                                <span class="todo-item" :class="{'has-line-through' : todo.isCompleted}">{{todo.item}}</span>
                            </span>
                            <button class="btn-delete" @click="deleteFilter(todo.id)"> 
                                <img src='../assets/images/icon-cross.svg' :class="{'none': todo.isCompleted}" />
                            </button>
                        </div>
                        <div class="underline"></div>
                    </li>
                </transition-group>
                <transition-group v-else  name="list" tag="ul" class="todo-list">
                    <li v-for="todo in todos" :key="todo.id" class="list-item">
                        <div class="todo-div">
                            <span class="todo">
                                <button class="btn-complete" @click="completeTodo(todo)"> 
                                    <img src='../assets/images/icon-check.svg' :class="{'none': todo.isCompleted}" />
                                </button>
                                <span class="todo-item" :class="{'has-line-through' : todo.isCompleted}">{{todo.item}}</span>
                            </span>
                            <button class="btn-delete" @click="deleteTodo(todo.id)"> 
                                <img src='../assets/images/icon-cross.svg' :class="{'none': todo.isCompleted}" />
                            </button>
                        </div>
                        <div class="underline"></div>
                    </li>
                </transition-group>
                <div class="todo-footer">
                    <div v-if="isFiltered" class="opacity-50">{{ filteredTodos.length }} item<span :class="{'none': filteredTodos.length<=1}">s</span> left</div>
                    <div v-else class="opacity-50">{{ todos.length }} item<span :class="{'none': todos.length<=1}">s</span> left</div>
                    <div class="desktop">
                        <button :class="{'active': filterStatus === 'All' }" @click="allTodos">All</button>
                        <button :class="{'active': filterStatus === 'Active' }" @click="activeFilter">Active</button>
                        <button :class="{'active': filterStatus === 'Completed' }" @click="completedFilter">Completed</button>
                    </div>
                    <div>
                        <button class="opacity-50" @click="clearCompletedTodos">
                            Clear Completed
                        </button>
                    </div>
                </div>                
            </div>
            <div class="todo-footer mobile">
                <button :class="{'active': filterStatus === 'All' }" @click="allTodos">All</button>
                <button :class="{'active': filterStatus === 'Active' }" @click="activeFilter">Active</button>
                <button :class="{'active': filterStatus === 'Completed' }" @click="completedFilter">Completed</button>
            </div>
        </div>
        </div>
    </div>
</template>

<script>
import Button from './Button.vue';
export default {
    name: "Todo",
    components: { Button },
    data(){
        return {
            title: "TODO",
            todos: [{item: "Welcome to Rahmlad TODO App", id: 1, isCompleted: false}, 
                {item: "To get started, create a new todo", id: 2, isCompleted: false},
                {item: "Once done, mark it as completed", id: 3, isCompleted: false}],
            filteredTodos: [{item: "Welcome to Rahmlad TODO App", id: 1, isCompleted: false}, 
                {item: "To get started, create a new todo", id: 2, isCompleted: false},
                {item: "Once done, mark it as completed", id: 3, isCompleted: false}],
            item: "",
            isdarkMode: false,
            isFiltered: false,
            filterStatus: 'All'
        }
    },
    methods: {
        toggle(mode){
            this.isdarkMode = mode
            localStorage.setItem('darktheme', mode)
        },
        addTodo(){
            if(this.isFiltered){
                this.isFiltered = false
            }
            this.todos.unshift({item: this.item, id:this.todos.length+1, isCompleted: false}),
            this.item = ""
            this.filterStatus = 'All'
        },
        deleteTodo(id){
            console.log()
            this.todos = this.todos.filter(todo=>todo.id != id)
        },
        completeTodo(todo){
            if(this.isFiltered){
                this.isFiltered = false
            }
            todo.isCompleted = !todo.isCompleted
        },
        clearCompletedTodos(){
            if(this.todos){
                this.todos = this.todos.filter(todo=> !todo.isCompleted);
            }
        },
        allTodos(){
            if(this.isFiltered){
                this.isFiltered = !this.isFiltered
            }
            this.filterStatus = 'All'
        },
        activeFilter(){
            if(!this.isFiltered){
                this.isFiltered = true
            }
            this.filteredTodos = this.todos.filter(todo=> !todo.isCompleted)
            this.filterStatus = 'Active';
        },
        completedFilter(){
            if(!this.isFiltered){
                this.isFiltered = true
            }
            this.filteredTodos = this.todos.filter(todo=> todo.isCompleted)
            this.filterStatus = 'Completed';
        },
        deleteFilter(id){
            this.filteredTodos = this.todos.filter(todo=>todo.id != id)
        },
        completeFilter(todo){
            todo.isCompleted = !todo.isCompleted
        }
    }, 
    watch: {
        todos: {
            handler(){
                localStorage.setItem('todos', JSON.stringify(this.todos));
            },
            deep: true
        }
    },
    mounted() {
        const localStorageTheme = localStorage.getItem('darktheme')
        const localStorageTodos = localStorage.getItem('todos')
        
        if(localStorageTodos){
            this.todos = JSON.parse(localStorageTodos);
        }
        
        if(localStorageTheme){
            this.isdarkMode = JSON.parse(localStorageTheme);    
        }
    }
}
</script>

<style scoped>

.has-line-through {
    text-decoration: line-through;
    opacity: 40%;
}
</style>