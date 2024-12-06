<script setup lang="ts">
import {useFilmStore} from "~/stores/film";
import {useCategoryStore} from "~/stores/category";
import {useCountryStore} from "~/stores/country";

const categoryStore = useCategoryStore();
const filmStore = useFilmStore()
const countryStore = useCountryStore();
const category = ref(null);
const country = ref(null);
const sort = ref('name');

const filter = () => {
  filmStore.addCategoryToParams(category.value);
  filmStore.addCountryToParams(country.value);
  filmStore.addSortToParams(sort.value);
  filmStore.currentPage = 1;
  filmStore.fetchFilms();
}
const reset = () => {
  category.value = ref(null);
  country.value = ref(null);
  sort.value = ref('name');
  filter();
}
const goto = (page: number) => {
  page = (page < 1) ? 1 : page;
  page = (page >filmStore.countPages) ? filmStore.countPages : page;
  if (page <= filmStore.countPage && page > 0) {
    filmStore.currentPage = page;
    filmStore.fetchFilms();
  }
}
</script>

<template>
  <div class="row mt-2">
    <div class="col-md-4">
      <select v-model="category" @change="filter" class="form-select" aria-label="Default select example">
        <option :value="null" selected>Select genre</option>
        <option v-for="category in categoryStore.categories"
                :key="category.id"
                :value="category.id">
          {{ category.name }} {{ category.filmCount }}
        </option>
      </select>
    </div>
    <div class="col-md-4">
      <select v-model="country" @change="filter" class="form-select" aria-label="Default select example">
        <option :value="null" selected>All Countries</option>
        <option
            v-for="country in countryStore.countries"
            :key="country.id"
            :value="country.id">
          {{ country.name }}
        </option>
      </select>
    </div>
    <div class="col-md-2">
      <select v-model="sort" @change="filter" class="form-select" aria-label="Default select example">
        <option value="1">Name</option>
        <option value="2">Year</option>
        <option value="3">Rating</option>
      </select>
    </div>
    <div class="col-md-2">
      <button type="button" class="btn btn-outline-info" @click="reset">Reset</button>
    </div>
  </div>
  <div v-if="!filmStore.isLoading" class="row row-cols-1 row-cols-md-3 g-4 mt-2">
    <div class="col" v-for="film in filmStore.films" :key="film.id">
      <div class="card h-100">
        <img v-if="film.link_img" :src="film.link_img" class="card-img-top" alt="...">
        <img v-else="film.link_img" src="https://play-lh.googleusercontent.com/1iPbCcMP7Exr2kewOQdhhF7esQUYbH0MgR1mAd0j5QF5MQ137nYrSqgM5ZaFsD87Qw"
             class="card-img-top" alt="...">
        <div class="card-body">
          <h5 class="card-title"> {{ film.name }}</h5>
          <p class="card-text"> {{ parseFloat(film.ratingAvg).toFixed(1) }}</p>
          <p class="card-text"> {{ film.duration }} min.</p>
          <p class="card-text"> {{ film.year_of_issue }} year</p>
          <p class="card-text">
            <template v-for="(category, index) in film.categories" :key="category.id">
              {{ category.name + (index + 1 < film.categories.length ? ", " : "") }}
            </template>
          </p>
        </div>
        <button type="button" class="btn btn-success">Add</button>
      </div>
    </div>
  </div>
  <div v-else class="d-flex justify-content-center mt-4">
    <div class="spinner-border" role="status">
      <span class="visually-hidden">Loading...</span>
    </div>
  </div>
  <nav class="mt-4 d-flex justify-content-center" aria-label="Page navigation example">
    <ul class="pagination">
      <li class="page-item">
        <a class="page-link" @click.prevent="goto(filmStore.currentPage - 1)" aria-label="Previous">
          <span aria-hidden="true">&laquo;</span>
        </a>
      </li>
      <li v-for="num in filmStore.countPage" :key="num"
          :class="['page-item', { 'active': filmStore.currentPage === num }]"
          @click.prevent="goto(num)">
        <a class="page-link" href="#">{{ num }}</a>
      </li>
      <li class="page-item">
        <a class="page-link" @click.prevent="goto(filmStore.currentPage + 1)" aria-label="Next">
          <span aria-hidden="true">&raquo;</span>
        </a>
      </li>
    </ul>
  </nav>

</template>

<style scoped>
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f8f9fa;
  color: #343a40;
  padding: 2rem;
}
.form-select {
  border-radius: 0.25rem;
  border: 1px solid #ced4da;
  padding: 0.5rem 1rem;
  transition: border-color 0.3s;
}
.form-select:focus {
  border-color: #80bdff;
  outline: none;
}
.btn-outline-info {
  color: #343a40;
  border: 1px solid #343a40;
}
.btn-outline-info:hover {
  background-color: #343a40;
  color: #f8f9fa;
}
.card {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border: none;
  border-radius: 0.5rem;
  overflow: hidden;
}
.card-img-top {
  height: 300px;
  object-fit: cover;
}
.card-body {
  padding: 1rem;
}
.card-title {
  font-size: 1.25rem;
  margin-bottom: 0.5rem;
}
.card-text {
  margin-bottom: 0.5rem;
}
.btn-success {
  background-color: #28a745;
  border: none;
  color: #f8f9fa;
  transition: background-color 0.3s;
}
.btn-success:hover {
  background-color: #218838;
}
.spinner-border {
  width: 3rem;
  height: 3rem;
  border-width: 0.3rem;
}
.pagination .page-item .page-link {
  color: #343a40;
}
.pagination .page-item .page-link:hover {
  background-color: #343a40;
  color: #f8f9fa;
}
.pagination .page-item.active .page-link {
  background-color: #343a40;
  border-color: #343a40;
  color: #f8f9fa;
}
</style>
