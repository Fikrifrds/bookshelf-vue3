<template>
  <h2>BookSelf</h2>
  <form @submit.prevent="addBook">
    <label for="title">Judul Buku</label>
    <input autocomplete="off" id="title" v-model="title" />
    <label for="image">Gambar</label>
    <input autocomplete="off" id="image" v-model="image" />
    <button>Tambah</button>
  </form>
    <h4>Akan Dibaca</h4>
    <div class="book-item" v-for="book in toReadBooks" :key="book.id">
      <img :src="book.image" width="80" />
      <div>
        <p>{{ book.title }}</p>
        <button @click="updateStatus(book, 0)">Baca buku ini</button>
      </div>
    </div>

    <h4>Sedang Dibaca</h4>
    <div class="book-item" v-for="book in readingBooks" :key="book.id">
      <img :src="book.image" width="80" />
      <div>
        <p>{{ book.title }}</p>
        <button @click="updateStatus(book, 1)">Sudah dibaca</button>
      </div>
    </div>

    <h4>Sudah Dibaca</h4>
    <div class="book-item" v-for="book in haveReadBooks" :key="book.id">
      <img :src="book.image" width="80" />
      <p>{{ book.title }}</p>
    </div>

</template>

<script>
import { ref, computed, onMounted, watch } from 'vue';
export default {
  name: 'App',
  setup() {
    const title = ref('');
    const image = ref('');
    const books = ref([]);

    function addBook() {
      const newBook = {
        id: Date.now(),
        title: title.value,
        image: image.value,
        status: -1 // -1 akan dibaca, 0 sedang dibaca, 1 sudah dibaca
      }

      books.value.push(newBook);
      localStorage.books = JSON.stringify(books.value);
      title.value = '';
      image.value = '';
    }

    function updateStatus(book, status) {
      book.status = status;
    }

    const toReadBooks = computed(() => books.value.filter( book => book.status === -1));
    const readingBooks = computed(() => books.value.filter( book => book.status === 0));
    const haveReadBooks = computed(() => books.value.filter( book => book.status === 1));

    onMounted(() => {
      const bookData = localStorage.books;
      if (bookData) {
        books.value = JSON.parse(bookData);
      }
    });

    watch(readingBooks, (val, oldVal) => {
      if (val.length > 3) {
        alert('Anda sudah membaca banyak buku, sebaiknya perlu istirahat sejenak!')
      }
    })

    return {
      title,
      image,
      books,
      addBook,
      toReadBooks,
      readingBooks,
      haveReadBooks,
      updateStatus
    }
  }
}
</script>

<style scoped>
  form {
    display: flex;
    flex-direction: column;
    width: 50%;
  }

  input {
    height: 30px;
    margin-bottom: 10px;
  }

  .book-item {
    display: flex;
  }

  img {
    margin: 10px;
  }

  h4 {
    border-bottom: 1px solid gray;
  }
  
</style>