<template>
	<div id="app">
	  <!-- Приветственный блок -->
	  <div class="greeting">
		<h2>Севилечка, привет, выбирай любое блюдо, но только смотри внимательно на цены!</h2>
	  </div>
  
	  <h1 v-if="!isAuthenticated">Авторизация</h1>
	  <div v-if="!isAuthenticated">
		<form @submit.prevent="loginFunc">
		  <div>
			<label for="login">Логин</label>
			<input v-model="login" type="text" id="login" required />
		  </div>
		  <div>
			<label for="password">Пароль</label>
			<input v-model="password" type="password" id="password" required />
		  </div>
		  <button type="submit">Войти</button>
		</form>
		<p v-if="authError" style="color: red;">Неверный логин или пароль!</p>
	  </div>
  
	  <div v-if="isAuthenticated">
		<h1>Меню</h1>
		<div v-for="dish in dishes" :key="dish.name" class="dish-card">
		  <h2>{{ dish.name }}</h2>
		  <p>Цена: {{ dish.price }}</p>
		  <button @click="orderDish(dish)">Заказать</button>
		</div>
		<div v-if="orderStatus" class="status">
		  <p>{{ orderStatus }}</p>
		</div>
	  </div>
	</div>
  </template>
  
  <script lang="ts">
  import { defineComponent, ref } from 'vue';
  import axios from 'axios';
  
  interface Dish {
	name: string;
	price: string;
  }
  
  export default defineComponent({
	name: 'App',
	setup() {
	  const dishes = ref<Dish[]>([]);
	  const orderStatus = ref<string>('');
	  const login = ref<string>('');
	  const password = ref<string>('');
	  const authError = ref<boolean>(false);
	  const isAuthenticated = ref<boolean>(false);
  
	  const getDishes = async () => {
		try {
		  const response = await axios.get('http://localhost:8080/dishes');
		  dishes.value = response.data;
		} catch (error) {
		  console.error('Ошибка при загрузке меню:', error);
		}
	  };
  
	  const loginUser = async () => {
		try {
		  const response = await axios.post('http://localhost:8080/auth', {
			login: login.value,
			password: password.value,
		  });
  
		  if (response.status === 200) {
			isAuthenticated.value = true;
			getDishes(); // Загружаем меню после успешной аутентификации
		  } else {
			authError.value = true;
		  }
		} catch (error) {
		  console.error('Ошибка аутентификации:', error);
		  authError.value = true;
		}
	  };
  
	  const loginFunc = async () => {
		authError.value = false;
		await loginUser();
	  };
  
	  const orderDish = async (dish: Dish) => {
		try {
		  await axios.post('http://localhost:8080/notify', {
			dish_name: dish.name,
			dish_price: dish.price,
		  });
		  orderStatus.value = `Вы заказали: ${dish.name} за ${dish.price}`;
		} catch (error) {
		  console.error('Ошибка при оформлении заказа:', error);
		  orderStatus.value = 'Ошибка при оформлении заказа!';
		}
	  };
  
	  return {
		dishes,
		orderStatus,
		orderDish,
		loginFunc,
		authError,
		isAuthenticated,
		login,
		password,
	  };
	},
  });
  </script>
  
  <style scoped>
  #greeting {
	background-color: #ffeb3b;
	color: #333;
	padding: 20px;
	margin-bottom: 20px;
	text-align: center;
	border-radius: 10px;
	font-size: 18px;
	font-weight: bold;
	box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
  }
  
  .dish-card {
	display: inline-block;
	width: 220px;
	margin: 10px;
	padding: 20px;
	text-align: center;
	background-color: #fff;
	border: 1px solid #ccc;
	border-radius: 10px;
	box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
  .dish-card h2 {
	font-size: 20px;
	margin: 10px 0;
  }
  
  .dish-card p {
	font-size: 16px;
	color: #888;
  }
  
  button {
	padding: 8px 16px;
	background-color: #4CAF50;
	color: white;
	border: none;
	cursor: pointer;
	border-radius: 5px;
  }
  
  button:hover {
	background-color: #45a049;
  }
  
  .status {
	margin-top: 20px;
	font-weight: bold;
	color: #ff0000;
  }
  
  form {
	max-width: 300px;
	margin: 0 auto;
  }
  
  form div {
	margin-bottom: 15px;
  }
  
  input {
	padding: 8px;
	width: 100%;
  }
  
  button[type="submit"] {
	width: 100%;
	padding: 10px;
	background-color: #4CAF50;
	color: white;
	border: none;
	cursor: pointer;
  }
  
  button[type="submit"]:hover {
	background-color: #45a049;
  }
  </style>
  