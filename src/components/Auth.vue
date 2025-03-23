<script setup lang="ts">
import { ref, onMounted } from "vue";

const user = ref<{ id: number; first_name: string } | null>(null);

onMounted(() => {
  const savedUser = localStorage.getItem("tg_user");
  if (savedUser) {
    user.value = JSON.parse(savedUser);
  }
});

const handleAuth = (event: MessageEvent) => {
  if (event.origin !== "https://telegram.org") return;

  try {
    const data = JSON.parse(event.data);
    localStorage.setItem("tg_user", JSON.stringify(data));
    user.value = data;
  } catch (error) {
    console.error("Ошибка авторизации:", error);
  }
};

window.addEventListener("message", handleAuth);
</script>

<template>
  <div v-if="user">
    <p>Привет, {{ user.first_name }}! 💖</p>
  </div>
  <div v-else>
    <p>Войдите через Telegram:</p>
    <div id="tg-login"></div>
  </div>
</template>
