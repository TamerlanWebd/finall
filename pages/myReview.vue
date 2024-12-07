<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { useAuthStore } from '~/stores/auth';
import { useReviewStore } from '~/stores/review';
import {api} from "~/api";
const authStore = useAuthStore();
const reviewStore = useReviewStore();
const activeTab = ref('reviews');
const fetchUserReviews = async () => {
  if (authStore.authData?.id) {
    try {
      await reviewStore.fetchReviewsByUserId(authStore.authData.id);
    } catch (e) {
      console.error("Ошибка при получении отзывов:", e);
    }
  }
};
const deleteReview = async (reviewId) => {
  try {
    await reviewStore.deleteReview(authStore.authData?.id, reviewId);
    fetchUserReviews();
  } catch (e) {
    console.error("Ошибка при удалении отзыва:", e.response ? e.response.data : e.message);
  }
};
const user = ref(null);
const fetchUserData = async () => {
  try {
    const res = await api.get(`/users/${authStore.authData.id}`, {
      headers: {
        Authorization: `Bearer ${authStore.authData.token}`,
      },
    });
    user.value = res.data;
  } catch (error) {
    console.error('Failed to fetch user data:', error);
  }
};
onMounted(() => {
  fetchUserData();
});
onMounted(fetchUserReviews);
</script>

<template>
  <div class="profile-page">
    <header class="header">
      <div class="user-controls">
        <span>{{ user?.fio || 'User' }}</span>
      </div>
    </header>
    <main class="content">
      <section class="stats">
        <p>FIO: {{ user?.fio }}</p>
        <p>COUNT SCORES: {{ user?.ratingCount }}</p>
        <p>COUNT REVIEWS: {{ user?.reviewCount }}</p>
      </section>
      <section class="profile">
        <nav class="tabs">
          <button @click="$router.push('/profile')">MY PROFILE</button>
          <button @click="$router.push('/myReview')">MY REVIEWS</button>
          <button @click="$router.push('/myScores')">MY SCORES</button>
        </nav>
      </section>
    </main>
  </div>
  <div v-show="activeTab === 'reviews'" class="tab-pane show">
    <div v-if="reviewStore.reviews.length">
      <ul class="review-list">
        <li v-for="review in reviewStore.reviews" :key="review.id" class="review-item">
          <div class="review-details">
            <h5 class="review-title">{{ review.film.name }}</h5>
            <p class="review-text">Wrote:{{ user?.fio  }}</p>
            <p class="review-text">{{ review.message }}</p>
            <p class="review-date"><small>Date: {{ review.created_at }}</small></p>
          </div>
          <button class="btn btn-danger" @click="deleteReview(review.id)">Delete</button>
        </li>
      </ul>
    </div>
    <p v-else>No reviews yet.</p>
  </div>
</template>

<style scoped>
.profile-page {
  background-color: #f8f9fa;
  font-family: 'Helvetica Neue', sans-serif;
}
.header {
  background-color: #343a40;
  color: #ffffff;
  padding: 10px;
}
.user-controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.content {
  padding: 20px;
}
.stats {
  background-color: #ffffff;
  border-radius: 8px;
  padding: 15px;
  margin-bottom: 20px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.profile {
  background-color: #ffffff;
  border-radius: 8px;
  padding: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.tabs {
  display: flex;
  justify-content: space-around;
  margin-bottom: 20px;
}
.tabs button {
  flex: 1;
  padding: 10px;
  border: none;
  border-radius: 4px;
  background-color: #6c757d;
  color: #ffffff;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
.tabs button.active {
  background-color: #007bff;
}
.tabs button:hover:not(.active) {
  background-color: #5a6268;
}
.tab-content {
  animation: fadeIn 0.3s ease-in;
}
.review-item {
  background-color: #f8f9fa;
  border-radius: 4px;
  padding: 10px;
  margin-bottom: 10px;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}
.comments {
  margin-top: 10px;
  padding-left: 20px;
}
.actions {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}
.actions .delete {
  background-color: #dc3545;
  border: none;
  color: #ffffff;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
.actions .delete:hover {
  background-color: #bd2130;
}
.actions button {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
body {
  background-color: #e9ecef;
  padding: 30px;
  font-family: 'Helvetica Neue', sans-serif;
}
.review-list {
  list-style-type: none;
  padding: 0;
}
.review-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #ddd;
  padding: 15px 0;
}
.review-details {
  flex: 1;
  padding-right: 15px;
}
.review-title {
  font-size: 1.25rem;
  font-weight: bold;
  margin: 0;
}
.review-text,
.review-date {
  margin: 5px 0;
}
.btn {
  margin-left: 10px;
  padding: 10px 20px;
  font-size: 1rem;
  border: none;
  border-radius: 4px;
  transition: background-color 0.3s ease;
}
.btn-danger {
  background-color: #dc3545;
  color: #fff;
}
.btn-danger:hover {
  background-color: #bd2130;
}
</style>
