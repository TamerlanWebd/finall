<template>
  <nav class="navbar navbar-expand-lg bg-body-tertiary">
    <div class="container-fluid">
      <a class="navbar-brand" href="/">Logo KInotower</a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
          <li class="nav-item dropdown">
            <ul class="dropdown-menu">
              <li><a class="dropdown-item" href="#">Action</a></li>
              <li><a class="dropdown-item" href="#">Another action</a></li>
              <li><hr class="dropdown-divider"></li>
              <li><a class="dropdown-item" href="#">Something else here</a></li>
            </ul>
          </li>

        </ul>
        <template v-if="!authStore.authData">
          <button @click="$router.push('/signup')" class="btn btn-outline-success me-2" type="submit">SignUp</button>
          <button @click="$router.push('/signin')" type="button" class="btn btn-outline-info">SignIn</button>
        </template>
        <template v-else>
          <button @click="$router.push('/profile')"  class="btn btn-outline-info">{{user?.fio }}</button>
          <button @click="logout" type="button" class="btn btn-outline-danger">Quit</button>
        </template>
      </div>
    </div>
  </nav>
  <div class="container">
    <slot />
  </div>
  <footer class="bg-dark text-white p-3 mt-3">
    <div class="container text-center">
      <p class="lead">COPYRIGHT</p>
    </div>
  </footer>
</template>

<script setup lang="ts">
import { useAuthStore } from "#imports";
import { useRouter } from "vue-router";
import {onMounted, ref} from "vue";
import {api} from "~/api";

const authStore = useAuthStore();
const router = useRouter();

const logout = async () => {
  try {
    await authStore.signout();
    await router.push('/');
  } catch (error) {
    console.error('Error during sign out:', error);
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
</script>
<style scoped>
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f8f9fa;
  color: #343a40;
}
.navbar {
  background-color: #343a40;
}
.navbar-brand {
  color: lawngreen !important;
  font-weight: bold;
  font-size: 1.5rem;
}
.navbar-nav .nav-item .dropdown-menu {
  background-color: #343a40;
  border: none;
}
.navbar-nav .nav-item .dropdown-item {
  color: #f8f9fa;
}
.navbar-nav .nav-item .dropdown-item:hover {
  background-color: #495057;
}
.navbar-toggler {
  border: none;
}
.navbar-toggler-icon {
  background-color: #f8f9fa;
}
.navbar-collapse {
  justify-content: space-between;
}
.btn-outline-success,
.btn-outline-info,
.btn-outline-danger {
  color: #343a40; /* Black */
  border: 1px solid #343a40;
}
.btn-outline-success:hover,
.btn-outline-info:hover,
.btn-outline-danger:hover {
  background-color: #343a40;
  border: 1px solid #343a40;
}
.navbar-link {
  color: deepskyblue;
  margin-right: 1rem;
}
.navbar-link:hover {
  color: #adb5bd;
}
.container {
  margin-top: 2rem;
}
footer {
  background-color: #343a40;
  color: #f8f9fa;
  padding: 1rem 0;
  text-align: center;
  border-top: 3px solid #495057;
}
footer .lead {
  font-size: 1.2rem;
  font-weight: bold;
  margin: 0;
}
</style>
