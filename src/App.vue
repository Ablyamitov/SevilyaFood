<template>
	<div id="app">
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
		<div class="greeting">
			<h1>Севилечка, привет!</h1>
			<h1>Заказывай любое блюдо, но только смотри внимательно на цены)))))</h1>
		</div>

		<div class="dishes-container">
		  <div v-for="dish in dishes" :key="dish.name" class="dish-card">
			<img :src="`/img/${dish.name}.jpg`" :alt="dish.name" class="dish-image" />
			<h2>{{ dish.name }}</h2>
			<p>Цена: {{ dish.price }}</p>
			<button @click="orderDish(dish)">Заказать</button>
		  </div>
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
  
	  dishes.value = [
	  {name: "Бутерброд", price: "1 поцелуй💋"},
	{name: "Пельмени", price: "2 поцелуя💋💋"},
	{name: "Салат греческий", price: "1 свидание👫"},]


  
	  const loginUser = async () => {
		try {
		  const response = await axios.post('https://c06e-77-238-232-197.ngrok-free.app/auth', {
			login: login.value,
			password: password.value,
		  });
  
		  if (response.status === 200) {
			isAuthenticated.value = true;
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
		  await axios.post('https://c06e-77-238-232-197.ngrok-free.app/notify', {
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
  /* Общие стили для фона */


  .greeting {
  font-family:'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  padding: 20px;
  margin-bottom: 20px;
  text-align: center;
  font-size: 18px;
  font-weight: bold;

  background-color: rgba(255, 255, 255, 0.5);
  color: rebeccapurple;
}
  
  #app {
	text-align: center;
	padding: 0px;
  }


  
  .dishes-container {
	display: flex;
	flex-wrap: wrap;
	justify-content: center;
	gap: 20px;
  }
  
  .dish-card {
	display: flex;
	flex-direction: column;
	align-items: center;
	border: 1px solid #ddd;
	padding: 20px;
	margin: 10px;
	border-radius: 10px;
	width: 250px;
	box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
	background-color: rgba(255, 255, 255, 0.7); /* Полупрозрачный фон карточки */
	transition: transform 0.3s ease;

  }
  
  .dish-card:hover {
	transform: scale(1.05);
  }
  
  .dish-image {
	width: 100%;
	height: 200px;
	object-fit: cover;
	border-radius: 10px;
  }
  
  button {
	padding: 10px 20px;
	background-color: #4CAF50;
	color: white;
	border: none;
	border-radius: 5px;
	cursor: pointer;
	margin-top: 10px;
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
	max-width: 350px;
	margin: 0 auto;
	padding: 20px;
	
	border-radius: 8px;
  }
  
  form div {
	margin-bottom: 15px;
  }
  
  input {
	padding: 10px;
	width: 100%;
	margin-top: 5px;
	border: 1px solid #ccc;
	border-radius: 5px;
  }
  
  button[type="submit"] {
	width: 100%;
	padding: 10px;
	background-color: #4CAF50;
	color: white;
	border: none;
	border-radius: 5px;
	cursor: pointer;
  }
  
  button[type="submit"]:hover {
	background-color: #45a049;
  }
  </style>
  