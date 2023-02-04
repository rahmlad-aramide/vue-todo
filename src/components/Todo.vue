<template>
    <div class="body" :class="{'dark':isdarkMode}">
        <div class="header-img"></div>
        <div class="main">
            <div class="head">
                <h1 class="title">{{title}}</h1>
                <button class="pointer" @click="toggle(!isdarkMode)">
                    <img v-if="isdarkMode" src="../assets/images/icon-moon.svg" alt="theme" />
                    <img v-else src="../assets/images/icon-sun.svg" alt="theme" />
                </button>
            </div>
            <div class="input-div">
                <button  @click="addTodo" :disabled="!item"></button>
                <input type="text" v-model="item" placeholder="Create a new todo..." />
            </div> 
            <div class="todo-body">
                <ul class="todo-list transition"> 
                    <!-- <li v-if="isFiltered" v-for="(todo, i) in filteredTodos" :key="i" class="list-item">
                        <div class="todo-div">
                            <span class="todo">
                                <button class="btn-complete" @click="completeTodo(todo)"> 
                                    <img src='../assets/images/icon-check.svg' :class="{'none': todo.isCompleted}" />
                                </button>
                                <span class="todo-item" :class="{'has-line-through' : todo.isCompleted}">{{todo.item}}</span>
                            </span>
                            <button class="btn-delete" @click="deleteTodo(todo)"> 
                                <img src='../assets/images/icon-cross.svg' :class="{'none': todo.isCompleted}" />
                            </button>
                        </div>
                        <div class="underline"></div>
                    </li> -->
                    <li v-for="todo in todos" :key="todo.id" class="list-item">
                        <div class="todo-div">
                            <span class="todo">
                                <button class="btn-complete" @click="completeTodo(todo)"> 
                                    <img src='../assets/images/icon-check.svg' :class="{'none': todo.isCompleted}" />
                                </button>
                                <span class="todo-item" :class="{'has-line-through' : todo.isCompleted}">{{todo.item}}</span>
                            </span>
                            <button class="btn-delete" @click="deleteTodo(todo)"> 
                                <img src='../assets/images/icon-cross.svg' :class="{'none': todo.isCompleted}" />
                            </button>
                        </div>
                        <div class="underline"></div>
                    </li>
                </ul>
                <div class="todo-footer" >
                    <div class="opacity-50">{{ todos.length }} item<span :class="{'none': todos.length<=1}">s</span> left</div>
                    <div>
                        <button @click="allTodos">All</button>
                        <button @click="activeFilter">Active</button>
                        <button @click="completedFilter">Completed</button>
                    </div>
                    <div>
                        <button class="opacity-50" @click="clearCompletedTodos">
                            Clear Completed
                        </button>
                    </div>
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
            todos: [{item: "Todo template item", id: 1, isCompleted: false}],
            filteredTodos: [{item: "Filtered Todo template", id: 1, isCompleted: false}],
            item: "",
            isdarkMode: false,
            isFiltered: false
        }
    },
    methods: {
        toggle(mode){
            this.isdarkMode = mode
            document.documentElement.className = mode;
            localStorage.setItem('darktheme', mode)
        },
        addTodo(){
            if(this.isFiltered){
                this.isFiltered = false
            }
            this.todos.push({item: this.item, id:this.todos.length+1, isCompleted: false}),
            this.item = ""
        },
        deleteTodo(id){
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
        // allTodos(){
        //     this.isFiltered = !this.isFiltered
        // },
        // activeFilter(){
        //     if(!this.isFiltered){
        //         this.isFiltered = true
        //     }
        //     this.filteredTodos = this.todos.filter(todo=> !todo.isCompleted)
        // },
        // completedFilter(){
        //     if(!this.isFiltered){
        //         this.isFiltered = true
        //     }
        //     this.filteredTodos = this.todos.filter(todo=> todo.isCompleted)
        // }

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
        const localStorageTodos = localStorage.getItem('todos')
        if(localStorageTodos){
            this.todos = JSON.parse(localStorageTodos);
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