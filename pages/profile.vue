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
          <button @click="activeTab = 'profile'" :class="{ active: activeTab === 'profile' }">MY PROFILE</button>
          <button @click="$router.push('/myReview')">MY REVIEWS</button>
          <button @click="$router.push('/myScores')">MY SCORES</button>
        </nav>
        <div v-if="activeTab === 'profile'" class="tab-content">
          <p>EMAIL: {{ user?.email }}</p>
          <p>BIRTHDAY: {{ user?.birthday }}</p>
          <p>GENDER: {{ user?.gender?.name }}</p>
          <div class="actions">
            <button @click="$router.push('/editProfile')">EDIT</button>
            <button class="delete" @click="deleteAccount">DELETE ACCOUNT</button>
          </div>
        </div>
        <div v-if="activeTab === 'reviews'" class="tab-content">
          <div v-for="review in reviews" :key="review.id" class="review-item">
            <p>{{ review.content }}</p>
            <div class="comments">
              <p v-for="comment in review.comments" :key="comment.id">{{ comment.content }}</p>
            </div>
          </div>
        </div>
        <div v-if="activeTab === 'scores'" class="tab-content">
          <p v-for="rating in ratings" :key="rating.id">{{ rating.value }}</p>
        </div>
      </section>
    </main>
  </div>
</template>
<script>
import { ref, onMounted } from 'vue';
import { useAuthStore } from '~/stores/auth';
import { useReviewStore } from '~/stores/review';
import { useRatingStore } from '~/stores/rating';
import { api } from '~/api/index.js';
import { useRouter } from 'vue-router';
export default {
  setup() {
    const router = useRouter();
    const authStore = useAuthStore();
    const reviewStore = useReviewStore();
    const ratingStore = useRatingStore();
    const user = ref(null);
    const activeTab = ref('profile');
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
    const deleteAccount = async () => {
      try {
        await authStore.deleteAccount();
        await router.push('/');
      } catch (e) {
        console.error("Ошибка при удалении аккаунта:", e.message);
      }
    };
    onMounted(() => {
      fetchUserData();
      reviewStore.fetchReviewsByUserId();
      ratingStore.fetchRatingByUserId();
    });
    return {
      user,
      activeTab,
      reviews: reviewStore.reviews,
      ratings: ratingStore.ratings,
      signout: authStore.signout,
      deleteAccount,
    };
  },
};
</script>
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
</style>
