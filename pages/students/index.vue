<template>
  <div>
    <h1>Список студентів</h1>

    <div>
      <!-- Пошук -->
      <input type="text" v-model="searchQuery" placeholder="Пошук студента" style="margin-bottom: 10px; color:black;">

      <!-- Таблиця зі списком студентів -->
      <table>
        <thead>
        <tr>
          <th>Ім'я</th>
          <th>Прізвище</th>
          <th>Група</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="student in paginatedStudents" :key="student.id">
          <td>{{ student.firstName }}</td>
          <td>{{ student.lastName }}</td>
          <td>{{ student.group }}</td>
        </tr>
        </tbody>
      </table>
    </div>

    <!-- Пагінація -->
    <button @click="previousPage" :disabled="currentPage === 1">Back</button>
    <span>Сторінка {{ currentPage }} з {{ totalPages }}</span>
    <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      students: [], // Список студентів
      searchQuery: '', // Пошуковий запит
      currentPage: 1, // Поточна сторінка
      itemsPerPage: 5 // Кількість елементів на сторінці
    };
  },
  computed: {
    // Обчислення загальної кількості сторінок
    totalPages() {
      return Math.ceil(this.filteredStudents.length / this.itemsPerPage);
    },
    // Фільтрація студентів за пошуковим запитом
    filteredStudents() {
      return this.students.filter(student =>
          student.firstName.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
          student.lastName.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
          student.group.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },

    // Відфільтрований та з пагінацією список студентів для поточної сторінки
    paginatedStudents() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.filteredStudents.slice(startIndex, endIndex);
    }
  },
  methods: {
    // Перехід на попередню сторінку
    previousPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
    // Перехід на наступну сторінку
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    }
  },
  mounted() {
    // Метод для отримання списку студентів (може бути AJAX-запит)
    // Приклад:
    this.students = [
      { id: 1, firstName: 'Аня', lastName: 'Стерник', group: 'КН-22' },
      { id: 2, firstName: 'Вероніка', lastName: 'Ошейко', group: 'КН-22' },
      { id: 3, firstName: 'Олександр', lastName: 'Мосійчук', group: 'КН-21' },
      { id: 4, firstName: 'Михайло', lastName: 'Лук’янчук', group: 'КН-22' },
      { id: 5, firstName: 'Назарій', lastName: 'Теренчук', group: 'КН-22' },
      { id: 6, firstName: 'Владислав', lastName: 'Пухалський', group: 'КН-22' },
      { id: 7, firstName: 'Ірина', lastName: 'Гнатюк', group: 'КН-22' },
      { id: 8, firstName: 'Андрій', lastName: 'Лавренюк', group: 'КН-21' },
    ];
  }
};
</script>

<style>
/* Стилі для таблиці */
table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  color: black;
  background-color: #f2f2f2;
}

/* Зміна кольору рядків при наведенні миші */
tr:hover {
  background-color: lightgray;
  color: black;
}
/* Стилі для кнопок */
button {
  width: 90px; /* фіксована ширина кнопки */
  height: 30px; /* фіксована висота кнопки */
  border: 2px solid white; /* біла рамка */
  background-color: lightgray; /* прозорий фон */
  color: black; /* колір тексту */
  font-size: 16px; /* розмір шрифту */
  cursor: pointer; /* зміна курсору при наведенні */
  transition: background-color 0.3s, color 0.3s; /* плавна анімація зміни кольору */
}

/* Зміна стилю кнопок при наведенні миші */
button:hover {
  background-color: white; /* зміна фону при наведенні */
  color: black; /* зміна коліру тексту при наведенні */
}

</style>
