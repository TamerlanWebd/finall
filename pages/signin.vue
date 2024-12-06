<script setup lang="ts">
import { useAuthStore } from "~/stores/auth";
import { ref } from "vue";
import { useRouter } from "vue-router";

const authStore = useAuthStore();
const router = useRouter();
const errorMessage = ref('');
const email = ref('');
const password = ref('');

const login = async () => {
  try {
    await authStore.signin({
      email: email.value,
      password: password.value,
    });
    await router.push('/');
  } catch (e: any) {
    errorMessage.value = e.message || 'Login failed';
  }
}
</script>
<template>
  <div class="login-container min-vh-100 d-flex align-items-center bg-light">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-md-6 col-lg-4">
          <div class="card shadow-sm border-0 rounded-4">
            <div class="card-body p-5">
              <div class="text-center mb-4">
                <h1 class="h3 fw-bold text-primary mb-3">Welcome Back</h1>
                <p class="text-muted">Sign in to continue to your account</p>
              </div>
              <form @submit.prevent="login">
                <div class="form-floating mb-3">
                  <input
                      v-model="email"
                      type="email"
                      class="form-control"
                      id="floatingInput"
                      placeholder="name@example.com"
                      required
                  >
                  <label for="floatingInput">Email address</label>
                </div>
                <div class="form-floating mb-3">
                  <input
                      v-model="password"
                      type="password"
                      class="form-control"
                      id="floatingPassword"
                      placeholder="Password"
                      required
                  >
                  <label for="floatingPassword">Password</label>
                </div>

                <div v-if="errorMessage" class="alert alert-danger mb-3" role="alert">
                  {{ errorMessage }}
                </div>
                <div class="d-flex justify-content-between align-items-center mb-3">
                  <div class="form-check">
                    <input
                        class="form-check-input"
                        type="checkbox"
                        value="remember-me"
                        id="flexCheckDefault"
                    >
                    <label class="form-check-label" for="flexCheckDefault">
                      Remember me
                    </label>
                  </div>
                  <a href="#" class="text-primary text-decoration-none">Forgot password?</a>
                </div>
                <button
                    class="btn btn-primary w-100 py-2 rounded-3"
                    type="submit"
                >
                  Sign In
                </button>
                <div class="text-center mt-3">
                  <p class="text-muted">
                    Don't have an account?
                    <a href="#" class="text-primary">Sign up</a>
                  </p>
                </div>
              </form>
            </div>
          </div>
          <div class="text-center mt-3 text-muted">
            <small>&copy; 2024 Your Company. All Rights Reserved.</small>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f8f9fa;
  color: #343a40;
}
.login-container {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  background-color: #f8f9fa;
}
.card {
  border: none;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}
.card-body {
  padding: 2rem;
}
.h3 {
  margin-bottom: 1rem;
  color: #007bff;
}
.text-muted {
  color: #6c757d !important;
}
.form-floating {
  margin-bottom: 1.5rem;
}
.form-control {
  border-radius: 0.25rem;
  border: 1px solid #ced4da;
  padding: 0.75rem 1rem;
  transition: border-color 0.3s;
}
.form-control:focus {
  border-color: #007bff;
  box-shadow: none;
  outline: none;
}
.form-check-input {
  margin-right: 0.5rem;
}
.form-check-label {
  margin-bottom: 0;
}
.btn-primary {
  background-color: #007bff;
  border: none;
  color: #fff;
  padding: 0.75rem 1rem;
  transition: background-color 0.3s;
}
.btn-primary:hover {
  background-color: #0056b3;
}
.alert-danger {
  background-color: #f8d7da;
  border-color: #f5c6cb;
  color: #721c24;
  border-radius: 0.25rem;
  padding: 0.75rem;
  margin-bottom: 1.5rem;
}
.text-primary {
  color: #007bff !important;
}
.text-primary:hover {
  text-decoration: underline;
  color: #0056b3 !important;
}
footer {
  margin-top: 2rem;
  text-align: center;
  color: #6c757d;
}
</style>
