
<script setup lang="ts">
import { ref } from 'vue';
import { useGenderStore } from '@/stores/gender';
import {useAuthStore} from "~/stores/auth";

const fio = ref('');
const email = ref('');
const password = ref('');
const birthday = ref('');
const gender = ref(0);
const genderStore = useGenderStore();
const authStore = useAuthStore();
const router = useRouter();
const errorMessage = ref('');

const register = async () => {
  if (!fio.value || !email.value || !password.value || !birthday.value || gender.value === 0) {
    console.log("Некоторые поля не заполнены или некорректны.");
    return;
  }

  try {
    await authStore.signup({
      fio: fio.value,
      email: email.value,
      password: password.value,
      birthday: birthday.value,
      gender_id: gender.value,
    });
    router.push('/');
  } catch (e: any) {
    errorMessage.value = e.message;
  }
};

</script>

<template>
  <div class="row">
    <div class="col-md-4"></div>
    <div class="col-md-4">
      <h1 align="center">Registration</h1>
      <form action="" @submit.prevent="register">
        <label for="fio" class="form-label">Full name</label>
        <input type="text" class="form-control" id="fio" v-model="fio">

        <div class="mb-3">
          <label for="email" class="form-label">Email address</label>
          <input type="email" class="form-control" id="email" v-model="email">
        </div>

        <div class="mb-3">
          <label for="password" class="form-label">Password</label>
          <input type="password" class="form-control" id="password" v-model="password">
        </div>

        <div class="mb-3">
          <label for="birthday" class="form-label">Birthday</label>
          <input type="date" class="form-control" id="birthday" v-model="birthday">
        </div>

        <div class="mb-3">
          <label for="gender" class="form-label">Gender</label>
          <select class="form-select" v-model="gender">
            <option value="0" selected>Gender</option>
            <option
                v-for="gender in genderStore.genders"
                :key="gender.id"
                :value="gender.id">{{ gender.name }}</option>
          </select>
        </div>

        <div>
          <button type="submit" class="btn btn-primary">Sign Up</button>
        </div>
      </form>
      <div class="alert alert-danger" role="alert" v-if="errorMessage">
        {{ errorMessage }}
      </div>
    </div>
  </div>
</template>
<style scoped>
body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #eceff1;
  color: #333;
}

.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

.form-container {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  padding: 2rem;
  max-width: 600px;
  width: 100%;
}

h1 {
  text-align: center;
  color: #007bff;
  margin-bottom: 1.5rem;
}

.form-label {
  color: #495057;
  margin-bottom: 0.5rem;
  font-weight: bold;
}

.form-control {
  border-radius: 4px;
  border: 1px solid #ced4da;
  padding: 0.75rem 1rem;
  transition: border-color 0.3s;
}

.form-control:focus {
  border-color: #007bff;
  box-shadow: none;
  outline: none;
}

.form-select {
  border-radius: 4px;
  border: 1px solid #ced4da;
  padding: 0.75rem 1rem;
  transition: border-color 0.3s;
}

.form-select:focus {
  border-color: #007bff;
  box-shadow: none;
  outline: none;
}

.btn-primary {
  background-color: #007bff;
  border: none;
  color: #fff;
  padding: 0.75rem 1rem;
  width: 100%;
  border-radius: 4px;
  transition: background-color 0.3s;
  margin-top: 1rem;
}

.btn-primary:hover {
  background-color: #0056b3;
}

.alert-danger {
  background-color: #f8d7da;
  border-color: #f5c6cb;
  color: #721c24;
  border-radius: 4px;
  padding: 0.75rem;
  margin-top: 1rem;
  text-align: center;
}

.form-floating {
  position: relative;
  margin-bottom: 1.5rem;
}

.form-floating label {
  position: absolute;
  top: 50%;
  left: 1rem;
  transform: translateY(-50%);
  transition: all 0.2s;
  background: #fff;
  padding: 0 0.25rem;
}

.form-floating input:focus + label,
.form-floating input:not(:placeholder-shown) + label {
  top: 0;
  font-size: 0.75rem;
  color: #007bff;
}

.text-center {
  text-align: center;
}

.mb-3 {
  margin-bottom: 1rem !important;
}
</style>
