<script setup>
import {ref, onMounted, watch, computed} from "vue";
//
const todos = ref([]);
const name = ref("");
const error = ref("none");

const input_content = ref("")
const input_category = ref(null);

const todos_ascending = computed(()=> todos.value.sort((a,b)=>b.createdAt - a.createdAt)
)



watch(name, (newVal)=> {
  return localStorage.setItem('name', newVal)
})

watch(todos, (newVal)=> {
  return localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true}
)

//
onMounted(()=>{
  name.value = localStorage.getItem('name') || "";
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];

})

// todo method
const addTodo = ()=>{ 
 if(input_content.value.trim() ==="" || input_category.value === null ){
  error.value="show"
    return null;
}
 // if everything else is good then
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done:false,
    createdAt: new Date().getTime()
  })
  // clear input content
  input_content.value = ''
  input_category.value = null
  // 
  error.value="none"
}

  // remove todos
const removeTodo = (todo)=>{
  todos.value = todos.value.filter((t)=>t !== todo)
}

const resetTodos= ()=>{
 localStorage.setItem('todos', "")
 document.location.reload();
}
</script>

<template>
  <main class="app">
  <section class="greeting">
    <h2 class="title">
      What's app, <input type="text" placeholder="Name here"
    v-model="name" />
    </h2>
  </section>

    <!-- create a todo section    -->
    <section class="create-todo">
      <h3>
        Create a new todo
      </h3>
      <form action="" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" placeholder="e.g do laundry..."
         v-model="input_content"/>

      
        <h4>Pick a category</h4>

        <div class="options">
          <!-- label 1-->
          <label for="category">
            <input type="radio"
                   name="category"  
                   value="business" 
                   v-model="input_category" 
            />
            <span class="bubble business"></span>
            <span class="label-text"> Business</span>

          </label>
          <!--          label 2-->
          <label for="category">
            <input type="radio"
                   name="category"
                  
                   value="personal"
                   v-model="input_category"
            />
            <span class="bubble personal"></span>
            <span class="label-text">Personal </span> 
          </label>
        </div>
        <p :class="`error-text ${error}`">Please task field can't be empty. Make sure task field is not empy and category is selected.</p>

        <input type="submit" value="Add Todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_ascending" :key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">
         
          <label>
            <input type="checkbox" v-model="todo.done"/>
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text"  v-model="todo.content" :title="`Click to edit ${todo.content}`"/>
          </div>
         
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">
              Delete
            </button>
          </div>
        
        </div>
      </div>
      <!-- clear todos -->
      <button type="reset" class="buttn" @click="resetTodos">
        Clear All Todos 
      </button>
    </section>
  </main>
</template>
