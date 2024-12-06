<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { useAuthStore } from '~/stores/auth';
import { useRatingStore } from '~/stores/rating';
import {api} from "~/api";
const authStore = useAuthStore();
const ratingStore = useRatingStore();
const activeTab = ref('scores');
const fetchUserScores = async () => {
  try {
    await ratingStore.fetchRatingByUserId();
    console.log("Загруженные оценки:", ratingStore.ratings);
  } catch (e) {
    console.error("Ошибка при получении оценок:", e);
  }
};
const deleteRating = async (ratingId) => {
  try {
    await ratingStore.deleteRating(authStore.authData?.id, ratingId);
    fetchUserScores();
  } catch (e) {
    console.error("Ошибка при удалении оценки:", e.response ? e.response.data : e.message);
  }
};const user = ref(null);
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
onMounted(() => {
  if (authStore.authData?.id && authStore.authData?.token) {
    fetchUserScores();
  }
});
</script>

<template>
  <div v-show="activeTab === 'scores'" class="tab-pane show">
    <div v-if="ratingStore.ratings.length > 0">
      <ul class="score-list">
        <li v-for="score in ratingStore.ratings" :key="score.id" class="score-item">
          <div class="score-details">
            <h5 class="score-title">{{ score.film.name }}</h5>
            <p class="score-text">Delivered: {{user?.fio }}</p>
            <p class="score-text">Star: {{ score.score }}</p>
            <p class="score-date"><small>Date: {{ score.created_at }}</small></p>
          </div>
          <button class="btn btn-danger" @click="deleteRating(score.id)">Remove</button>
        </li>
      </ul>
    </div>
    <p v-else>No scores yet.</p>
  </div>
</template>

<style scoped>
.score-list {
  list-style-type: none;
  padding: 0;
}

.score-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #ddd;
  padding: 15px 0;
}

.score-details {
  flex: 1;
  padding-right: 15px;
}

.score-title {
  font-size: 1.25rem;
  font-weight: bold;
  margin: 0;
}

.score-text, .score-date {
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
  background-color: #c82333;
}
</style>
