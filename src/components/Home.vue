<template>
	<div>
	  <h1>Добро пожаловать в наш ресторан!</h1>
	  <DishList />
	</div>
  </template>
  
  <script lang="ts">
  import { defineComponent } from 'vue';
  import DishList from '../components/DishList.vue';
  
  export default defineComponent({
	name: 'Home',
	components: {
	  DishList,
	},
  });
  </script>
  