<script setup lang="ts">
import { ref, onMounted } from "vue";
import axios from "axios";

interface Dish {
  id: number;
  name: string;
  price_kisses: number;
  image_url: string;
}

const url = "https://9f06-77-238-232-197.ngrok-free.app"

const dishes = ref<Dish[]>([]);

const fetchDishes = async () => {
  try {
    const response = await axios.get(url + "/dishes");
    dishes.value = response.data;
  } catch (error) {
    console.error("Ошибка загрузки блюд", error);
  }
};

onMounted(fetchDishes);

const orderDish = async (dishId: number) => {
  const user = JSON.parse(localStorage.getItem("tg_user") || "{}");
  if (!user.id) {
    alert("Сначала войдите через Telegram!");
    return;
  }

  try {
    await axios.post(url + "/orders", {
      user_id: user.id,
      dish_id: dishId,
    });
    alert("Заказ оформлен!");
  } catch (error) {
    console.error("Ошибка при заказе", error);
    alert("Ошибка при заказе");
  }
};

</script>

<template>
  <div class="dishes">
    <div v-for="dish in dishes" :key="dish.id" class="dish-card">
      <img :src="dish.image_url" alt="dish.name" />
      <h3>{{ dish.name }}</h3>
      <p>💋 {{ dish.price_kisses }} поцелуев</p>
      <button @click="orderDish(dish.id)">Заказать</button>
    </div>
  </div>
</template>

<style scoped>
.dishes {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.dish-card {
  border: 1px solid #ddd;
  padding: 15px;
  border-radius: 8px;
  text-align: center;
  max-width: 200px;
}

img {
  width: 100%;
  height: auto;
  border-radius: 8px;
}

button {
  margin-top: 10px;
  background-color: #ff69b4;
  color: white;
  border: none;
  padding: 10px;
  cursor: pointer;
  border-radius: 5px;
}
</style>
