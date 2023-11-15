<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const content = ref('')
const category = ref(null)

//sort: if a>b then return true else false 
//replace true as 1 and false as -1
//a-b will be possitive, if a>b
//a-b will be nagative, if a>b
const todos_sorted = computed(() => todos.value.sort((a,b) =>{
	//return a.createdAt - b.createdAt
	return (a.createdAt>b.createdAt) ? 1 : -1
	//return (a.content>b.content) ? -1 : 1
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
	if (content.value.trim() === '' || category.value === null) {
		return
	}

	todos.value.push({
		content: content.value,
		category: category.value,
		done: false,
		createdAt: new Date().getTime()
	})

	content.value=''
	category.value=null

	//console.log(todos);
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''

	//parse JSON string to JavaScript value or Object described by the string
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				สวัสดีค่ะ .. <input type="text" id="name" placeholder="Name here" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3>สร้างบันทึกรายการงาน</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>รายการงานที่ต้องการบันทึก</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="เช่น สอนวิชาการพัฒนาเว็บแอปพลิเคชัน หรือ จ่ายค่าบัตรเครดิต"
					v-model="content" />
				
				<h4>ประเภทรายการงาน</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="teaching"
							v-model="category" />
						<span class="bubble business"></span>
						<div>งานสอน</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="category" />
						<span class="bubble personal"></span>
						<div>ส่วนตัว</div>
					</label>

				</div>

				<input type="submit" value="เพิ่มไปยังรายการงาน" />
			</form>
		</section>

		<section class="todo-list">
			<h3>รายการงาน</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_sorted" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'teaching' 
								? 'teaching' 
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
