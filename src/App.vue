<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')
const input_title=ref('')
const input_decription = ref('')
const input_category = ref(null)
const endpoint_API = ref('http://localhost:8080/tasks')

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_decription.value.trim() === '' || input_category.value === null) {
		return
	}

	todos.value.push({
		content: input_decription.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				TO-DO TEST, <input type="text" id="name" placeholder="Julian Castellanos 1019024206" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3>LG TASKS</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's your task?</h4>			
				<input 
					type="text" 
					name="title" 
					id="title" 
					placeholder="title task"
					v-model="input_title" />
				
							<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="description"
					v-model="input_decription" />
				
				<h4>Pick a task state</h4>
				<div class="options">
					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Pending</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Complete</div>
					</label>
				</div>
				<input type="submit" value="Add LG task" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TASKS LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
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