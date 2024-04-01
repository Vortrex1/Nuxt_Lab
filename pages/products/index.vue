<template>
  <div>
    <h1>Список продуктів</h1>

    <div style="margin-bottom: 10px;">
      <!-- Пошук -->
      <input type="text" v-model="searchQuery" placeholder="Пошук продукта" style="color:black; ">

      <!-- Сортування та фільтрація -->
      <button @click="toggleSortingWindow" class="sort-filter-btn" style="width: 120px">Сортування</button>
      <div v-if="showSortingWindow" class="sorting-window">
        <div>
          <button @click="sortByPrice" class="sort-btn">Ціна</button>
          <button @click="sortByRating" class="sort-btn">Оцінка</button>
        </div>
        <div v-if="sortBy === 'price'">
          <select v-model="priceSortOrder">
            <option value="ascending">По зростанню</option>
            <option value="descending">По спаданню</option>

          </select>

        </div>
        <div v-if="sortBy === 'rating'">
          <select v-model="ratingSortOrder">
            <option value="ascending">По зростанню</option>
            <option value="descending">По спаданню</option>
          </select>
        </div>
        <button @click="applySortingAndFiltering" class="apply-btn" style="width: 110px">Застосувати</button>
        <button @click="refresh" class="refresh-btn">Оновити</button>

      </div>
    </div>

    <!-- Таблиця з продуктами -->
    <table class="product-table">
      <thead>
      <tr>
        <th>Назва</th>
        <th>Опис</th>
        <th>Ціна</th>
        <th>Оцінка</th>
        <th>Бренд</th>
        <th>Категорія</th>
        <th>Фото</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="product in paginatedProducts" :key="product.id">
        <td>{{ product.title }}</td>
        <td>{{ product.description }}</td>
        <td>{{ product.price }}</td>
        <td :style="{ color: product.rating < 4.5 ? 'red' : 'green' }">{{ product.rating }}</td>
        <td>{{ product.brand }}</td>
        <td>{{ product.category }}</td>
        <td><img :src="product.thumbnail" alt="Product Thumbnail" class="product-thumbnail"></td>
      </tr>
      </tbody>
    </table>

    <!-- Пагінація -->
    <div class="pagination">
      <button @click="previousPage" :disabled="currentPage === 1" class="pagination-btn"><</button>
      <template v-for="page in totalPages" :key="page">
        <button @click="changePage(page)" :class="{ active: page === currentPage }" class="pagination-btn">{{ page }}</button>
      </template>
      <button @click="nextPage" :disabled="currentPage === totalPages" class="pagination-btn">></button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [], // Список продуктів
      searchQuery: '', // Пошуковий запит
      currentPage: 1, // Поточна сторінка
      itemsPerPage: 4, // Кількість елементів на сторінці
      showSortingWindow: false,
      sortBy: '', // Поле для сортування
      priceSortOrder: 'ascending', // Напрямок сортування за ціною
      ratingSortOrder: 'ascending', // Напрямок сортування за оцінкою
      priceRangeMin: null, // Мінімальна ціна у виборі "від і до"
      priceRangeMax: null // Максимальна ціна у виборі "від і до"
    };
  },
  computed: {
    // Обчислення загальної кількості сторінок
    totalPages() {
      return Math.ceil(this.filteredProducts.length / this.itemsPerPage);
    },
    // Фільтрація та сортування продуктів
    filteredProducts() {
      let filtered = this.products.filter(product =>
          product.title.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
          product.description.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
          product.brand.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
          product.category.toLowerCase().includes(this.searchQuery.toLowerCase())
      );

      if (this.sortBy) {
        filtered.sort((a, b) => {
          const fieldA = a[this.sortBy];
          const fieldB = b[this.sortBy];
          let result = 0;
          if (fieldA < fieldB) result = -1;
          if (fieldA > fieldB) result = 1;
          if (this.sortBy === 'price' && this.priceSortOrder === 'descending') result *= -1;
          if (this.sortBy === 'rating' && this.ratingSortOrder === 'descending') result *= -1;
          return result;
        });
      }

      return filtered;
    },
    // Відфільтрований та з пагінацією список продуктів для поточної сторінки
    paginatedProducts() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.filteredProducts.slice(startIndex, endIndex);
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
    },
    // Відкриття або закриття вікна сортування та фільтрації
    toggleSortingWindow() {
      this.showSortingWindow = !this.showSortingWindow;
    },
    // Сортування за ціною
    sortByPrice() {
      this.sortBy = 'price';
    },
    // Сортування за оцінкою
    sortByRating() {
      this.sortBy = 'rating';
    },
    // Застосування сортування та фільтрації
    applySortingAndFiltering() {
      this.showSortingWindow = false;

      if (this.sortBy === 'price' && this.priceSortOrder === 'custom') {
        this.filteredProducts = this.products.filter(product =>
            product.price >= this.priceRangeMin && product.price <= this.priceRangeMax
        );
      } else {
        this.filteredProducts = this.products.filter(product =>
            product.title.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
            product.description.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
            product.brand.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
            product.category.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      }
    },

    refresh() {
      this.searchQuery = ''; // Очищаємо пошуковий запит
      this.currentPage = 1; // Повертаємося на першу сторінку
      this.showSortingWindow = false; // Закриваємо вікно сортування
      this.sortBy = ''; // Скидаємо сортування
      this.priceSortOrder = 'ascending'; // Скидаємо сортування за ціною
      this.ratingSortOrder = 'ascending'; // Скидаємо сортування за оцінкою
      this.priceRangeMin = null; // Скидаємо вибране значення мінімальної ціни
      this.priceRangeMax = null; // Скидаємо вибране значення максимальної ціни
      this.fetchProducts(); // Оновлюємо дані про продукти
    },

    async fetchProducts() {
      try {
        const response = await fetch('https://dummyjson.com/products');
        const data = await response.json();
        this.products = data.products;
      } catch (error) {
        console.error('Error fetching products:', error);
      }
    }
  },
  mounted() {
    this.fetchProducts();
  }
};

</script>

<style scoped>
/* Стилі для кнопок */
button.sort-filter-btn {
  background-color: lightgray;
  color: black;
  border: 2px solid white;
  border-radius: 4px;
  padding: 5px 10px;
  cursor: pointer;
  margin-left: 10px;
}

button.sort-filter-btn:hover {
  background-color: lightgrey;
}

button.sort-btn {
  background-color: lightblue;
  color: black;
  border: 2px solid white;
  border-radius: 4px;
  padding: 5px 10px;
  cursor: pointer;
  margin-right: 10px;
}

button.sort-btn:hover {
  background-color: lightcyan;
}

button.apply-btn {
  background-color: lightblue;
  color: black;
  border: 2px solid white;
  border-radius: 4px;
  padding: 5px 10px;
  cursor: pointer;
  margin-top: 10px;
}

button.apply-btn:hover {
  background-color: lightcyan;
}

button.pagination-btn {
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

button.pagination-btn:hover {
  background-color: white;
}

button.pagination-btn:disabled {
  background-color: lightgray;
  color: gray;
  cursor: not-allowed;
}

/* Стилі для вікна сортування та фільтрації */
.sorting-window {
  border: 1px solid lightgray;
  background-color: white;
  padding: 10px;
  border-radius: 4px;
  margin-top: 10px;
}

.sorting-window label {
  display: block;
  margin-bottom: 5px;
}

.sort-filter-select {
  margin-bottom: 10px;
}

.sort-filter-input {
  margin-bottom: 10px;
}

/* Стилі для таблиці продуктів */
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

/* Стилі для зображень продуктів */
.product-thumbnail {
  width: 100px;
  height: 100px;
}

/* Стилі для активної кнопки пагінації */
button.pagination-btn.active {
  background-color: lightblue;
}
.sort-filter-btn,
.sort-btn,
.apply-btn {
  padding: 8px 16px; /* Збільшення внутрішніх відступів */
}

.sort-filter-btn,
.sort-btn,
.apply-btn {
  padding: 12px 20px; /* Збільшення внутрішніх відступів */
  font-size: 16px; /* Збільшення розміру шрифту */
}

</style>
