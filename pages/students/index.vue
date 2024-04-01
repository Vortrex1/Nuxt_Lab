<template>
  <div>
    <h1>Список студентів</h1>

    <div style="margin-bottom: 20px;">
      <!-- Пошук -->
      <input type="text" v-model="searchQuery" placeholder="Пошук студента" style="margin-bottom: 10px; color: black;">

      <!-- Фільтрація за групами -->
      <div style="margin-bottom: 10px;">
        <label for="sortByGroup">Сортувати за групами: </label>
        <select v-model="sortByGroup" id="sortByGroup" class="sort-filter-select">
          <option value="">Не сортувати</option>
          <option value="ascending">За зростанням</option>
          <option value="descending">За спаданням</option>
        </select>
      </div>

      <!-- Фільтрація за прізвищами -->
      <div>
        <label for="sortByLastName">Сортувати за Прізвищами: </label>
        <select v-model="sortByLastName" id="sortByLastName" class="sort-filter-select">
          <option value="">Не сортувати</option>
          <option value="ascending">За алфавітом (за зростанням)</option>
          <option value="descending">За алфавітом (за спаданням)</option>
        </select>
      </div>
    </div>

    <!-- Таблиця зі списком студентів -->
    <table class="product-table">
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

    <!-- Пагінація -->
    <button @click="previousPage" :disabled="currentPage === 1" class="pagination-btn"><</button>
    <template v-for="page in totalPages" :key="page">
      <button @click="changePage(page)" :class="{ active: page === currentPage }" class="pagination-btn">{{ page }}</button>
    </template>
    <button @click="nextPage" :disabled="currentPage === totalPages" class="pagination-btn">></button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      students: [], // Список студентів
      searchQuery: '', // Пошуковий запит
      currentPage: 1, // Поточна сторінка
      itemsPerPage: 5, // Кількість елементів на сторінці
      sortByGroup: '', // Поле для сортування за групами
      groupByDescending: false, // Флаг для визначення напрямку сортування за групами
      sortByLastName: '', // Поле для сортування за прізвищами
      lastNameDescending: false // Флаг для визначення напрямку сортування за прізвищами
    };
  },
  computed: {
    // Обчислення загальної кількості сторінок
    totalPages() {
      return Math.ceil(this.filteredStudents.length / this.itemsPerPage);
    },
    // Фільтрація студентів за пошуковим запитом, групами та прізвищами
    filteredStudents() {
      let filtered = this.students.filter(student =>
          student.firstName.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
          student.lastName.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
          student.group.toLowerCase().includes(this.searchQuery.toLowerCase())
      );

      // Сортування за групами
      if (this.sortByGroup) {
        filtered.sort((a, b) => {
          const groupA = a.group.toLowerCase();
          const groupB = b.group.toLowerCase();
          let result = 0;
          if (groupA < groupB) result = -1;
          if (groupA > groupB) result = 1;
          if (this.groupByDescending) result *= -1;
          return result;
        });
      }

      // Сортування за прізвищами
      if (this.sortByLastName) {
        filtered.sort((a, b) => {
          const lastNameA = a.lastName.toLowerCase();
          const lastNameB = b.lastName.toLowerCase();
          let result = 0;
          if (lastNameA < lastNameB) result = -1;
          if (lastNameA > lastNameB) result = 1;
          if (this.lastNameDescending) result *= -1;
          return result;
        });
      }

      return filtered;
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
    },
    // Перехід на конкретну сторінку
    changePage(page) {
      this.currentPage = page;
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

<style scoped>
/* Стилі для таблиці */
.product-table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.product-table th, .product-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

.product-table th {
  color: black;
  background-color: #f2f2f2;
}

.product-table tr:hover {
  background-color: lightgray;
  color: black;
}

/* Стилі для кнопок пагінації */
.pagination-btn {
  width: 40px;
  height: 30px;
  background-color: lightgray;
  color: black;
  border: 2px solid white;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s, color 0.3s;
}

.pagination-btn:hover {
  background-color: white;
}

.pagination-btn:disabled {
  background-color: lightgray;
  color: gray;
  cursor: not-allowed;
}

.pagination-btn.active {
  background-color: lightblue;
}

/* Стилі для поля пошуку та фільтрації */
.sort-filter-select {
  margin-bottom: 10px;
  padding: 8px 16px;
  font-size: 16px;
}

</style>
