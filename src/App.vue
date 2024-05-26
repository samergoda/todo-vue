<script setup>
import { computed, onMounted, ref, watch } from 'vue';

const todoList = ref([]);
const name = ref('');

const inputContent = ref('');
const inputCategory = ref(null);

const todoAsync = computed(_=> todoList.value.sort((a,b)=> b.createdAt - a.createdAt))

watch(name,(newVal)=>{
  localStorage.setItem('name',newVal)
})

onMounted(()=>{
  name.value = localStorage.getItem('name') || '';
  todoList.value = JSON.parse(localStorage.getItem('todoList'))||[]
})


function addTodo(){
if(inputContent.value.trim()==='' || inputCategory===null){
  alert('You should enter your todo correct and choose the category right')
  return
}
todoList.value.push({
  content:inputContent.value,
  category:inputCategory.value,
  createdAt: new Date().getTime(),

})
  inputContent.value = '';
  inputCategory.value = null; 
}

function removeTodo(todo){
  todoList.value = todoList.value.filter(t=>t!==  todo)
}
watch(todoList,newVal=>{
  localStorage.setItem('todoList',JSON.stringify(newVal))
},{deep:true})
</script>

<template>
<main class="app">
  <section class="greeting">
    <h2 class="title" >
      What's up, <input type="text" placeholder="Name here" v-model="name"/>
    </h2>
  </section>

  <section class="create-todo">
    <h3>CREATE A TODO</h3>
    <form @submit.prevent="addTodo" action="">
        <h4>What's on your todo list?</h4>
        <input 
        type="text" 
        placeholder="e.g make a video" 
        v-model="inputContent"/>
        
        <h4>Pick a category</h4>

          <div class="options">
            <label>
              <input 
              type="radio"
              value="business"
              v-model="inputCategory"            
              name="category">
              <span class="bubble business"></span>
              <div>Business</div>
            </label>



            <label>
              <input 
              type="radio"
              value="personal"
              v-model="inputCategory"            
              name="category">
              <span class="bubble personal"></span>
              <div class="">Personal</div>
            </label>
          </div>
          <input type="submit" value="Add todo" />
    </form>
  </section>

<!-- DISPLAY TODO LIST -->
  <section class="todo-list">
			<h3>TODO LIST</h3>
      <div class="" v-if="todoList.length === 0">
        Enter your todo 😀
            </div>
			<div class="list" id="todo-list">

				<div v-for="todo in todoList" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>

			</div>
		</section>
</main>
</template>

