<template>
  <div>
    <header>
      <nav>
        <nuxt-link to="/">Главная</nuxt-link>
        <nuxt-link to="/about">О нас</nuxt-link>
      </nav>
    </header>
    <main>
      <nuxt-page />
    </main>
    <footer>
      <p>© 2024 Блог о технологиях</p>
    </footer>
  </div>
</template>

<script setup>
// Здесь вы можете добавить JavaScript, если нужно
</script>

<style scoped>
header {
  background-color: #f8f8f8;
  padding: 10px;
}

footer {
  text-align: center;
  padding: 20px;
  background-color: #f8f8f8;
}
</style>
