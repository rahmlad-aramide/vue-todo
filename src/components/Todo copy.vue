<template>
    <div class="body">
        <div class="header-img"></div>
        <div class="main">
            <div class="head">
                <h1 class="title">{{title}}</h1>
                <img src="../assets/images/icon-moon.svg" alt="theme" />
            </div>
            <div class="input-div">
                <button  @click="add" :disabled="!item"></button>
                <!-- <Button title="Add todo" @click="add" :disabled="!item" /> -->
                <input type="text" v-model="item" placeholder="Create a new todo..." />
            </div> 
            <div class="todo-body">
                <ul class="todo-list"> 
                    <li v-for="todo in todos" :key="todo.id" class="list-item">
                        <span>
                            <button @click="complete(todo)"><img src='../assets/images/icon-check.svg' :class="{'none': todo.isCompleted}" />{{todo.isCompleted ? 'Undo' : 'Complete'}}</button>
                            <span :class="{'has-line-through' : todo.isCompleted}">{{todo.item}}</span>
                        </span>
                        <Button title="Delete" @click="deleteTodo(todo.id)"/>
                        <!-- <Button @click="complete(todo)" > {{todo.isCompleted ? 'Undo' : 'Complete'}} </Button> -->
                    </li>
                </ul>
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
            todos: [{item: "Todo template", id: 1, isCompleted: false}],
            item: ""
        }
    },
    methods: {
        add(){
            this.todos.push({item: this.item, id:this.todos.length+1, isCompleted: false}),
            console.log(this.todos)
            this.item = "" 
        },
        deleteTodo(id){
            console.log(id)
            this.todos = this.todos.filter(todo=>todo.id != id)
        },
        complete(todo){
            todo.isCompleted = !todo.isCompleted
            // this.btnTitle = todo.isCompleted? "Undo" : "Complete"
            console.log(todo)
            console.log(this.btnTitle);
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
}
</style>