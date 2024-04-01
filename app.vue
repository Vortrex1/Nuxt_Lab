<template>
  <div id="app">
    <nav>
      <div class="nav-item">
        <router-link to="/students" class="nav-link">Students</router-link>
        <router-link to="/products" class="nav-link" style="margin-left: 10px;">Products</router-link>
      </div>
    </nav>
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App',
};
</script>

<style>
/* Стилізація для навігаційного меню */
nav {
  padding: 10px; /* Додайте відступи для кращого оформлення */
}

/* Стилізація для обгортки кожного елемента навігації */
.nav-item {
  display: inline-block; /* Розміщення по горизонталі один після одного */
}

/* Стилізація для кнопки "Students" */
.nav-link {
  display: inline-block; /* Розміщення по горизонталі один після одного */
  padding: 8px 12px; /* Оптимальні відступи від тексту до рамки */
  border: 2px solid transparent; /* Рамка невидима до наведення */
  border-radius: 4px; /* Закруглені кути */
  background-color: lightgray; /* Прозорий фон */
  color: black; /* Колір тексту */
  font-size: 16px; /* Розмір шрифту */
  text-decoration: none; /* Відміна підкреслення */
  transition: border-color 0.3s, background-color 0.3s, color 0.3s; /* Плавні анімації */
}

.nav-link:hover {
  border-color: darkgrey; /* Зміна кольору рамки при наведенні */
  background-color: lightgrey; /* Зміна кольору фону при наведенні */
  color: white; /* Зміна кольору тексту при наведенні */
}

/* Додаткові стилі */
.nav-item + .nav-item {
  margin-left: 10px; /* Відступ між елементами навігації по горизонталі */
}

.nav-link.active {

  font-weight: bold; /* Жирний текст для активного елемента */
}

</style>
