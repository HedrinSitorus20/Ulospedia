<template>
  <div class="grid grid-cols-2">
    <div class="col-span-1">
      <img src="../../assets/images/admin/login-illustration.png" alt="" class="h-screen w-full" />
    </div>

    <div class="form col-span-1">
      <div class="flex min-h-full flex-col justify-center px-6 py-12 lg:px-8">
        <div class="sm:mx-auto sm:w-full sm:max-w-sm">
          <img class="mx-auto w-20 h-14" src="../../assets/ditenun-logo.png" alt="DiTenun" />
          <h3 class="mt-6 text-center font-medium text-[26px]">Selamat Datang Kembali</h3>
          <p class="mt-2 text-center font-normal text-xl text-neutral_80">
            Masukkan kredensial yang valid untuk login
          </p>
        </div>

        <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-sm">
          <form class="space-y-8" @submit.prevent="loginUser">
            <div v-if="error" class="bg-red-100 text-red-500 px-4 py-2 mb-4 rounded-md flex justify-between items-center">
              <span>{{ error }}</span>
              <button type="button" @click="clearError" class="text-red-500 focus:outline-none">
                <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                </svg>
              </button>
            </div>

            <div>
              <label for="username" class="block mb-2 text-base font-medium text-neutral_100">Username</label>
              <div class="relative">
                <!-- Include SVG icon for username here -->
                <div class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="none">
                      <path
                        stroke="#404040"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="1.5"
                        d="M10 10a4.167 4.167 0 1 0 0-8.333A4.167 4.167 0 0 0 10 10ZM17.158 18.333c0-3.225-3.208-5.833-7.158-5.833s-7.158 2.608-7.158 5.833"
                      />
                    </svg>
                  </div>
                <input
                  v-model="username"
                  type="text"
                  id="username"
                  required
                  class="border border-primary_border text-neutral_90 text-base rounded-lg focus:border-primary_pressed block w-full pl-10 p-2.5"
                  placeholder="Masukkan username anda"
                  @input="clearError"
                />
                <span v-if="submitted && !username" class="absolute mt-1  text-red-500">Masukkan username anda</span>
              </div>
            </div>

            <div>
              <label for="password" class="block mb-2 text-base font-medium text-neutral_100">Password</label>
              <div class="relative">
                <!-- Include SVG icon for password here -->
                <div class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none">
                    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="none">
                      <path
                        stroke="#404040"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="1.5"
                        d="M5 8.333V6.667c0-2.759.833-5 5-5s5 2.241 5 5v1.666M10 15.417a2.083 2.083 0 1 0 0-4.167 2.083 2.083 0 0 0 0 4.167Z"
                      />
                      <path
                        stroke="#404040"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="1.5"
                        d="M14.167 18.333H5.833c-3.333 0-4.166-.833-4.166-4.166V12.5c0-3.333.833-4.167 4.166-4.167h8.334c3.333 0 4.166.834 4.166 4.167v1.667c0 3.333-.833 4.166-4.166 4.166Z"
                      />
                    </svg>
                  </div>
                <input
                  v-model="password"
                  id="password"
                  name="password"
                  type="password"
                  autocomplete="current-password"
                  required
                  class="border border-primary_border text-neutral_90 text-base rounded-lg focus:border-primary_main block w-full pl-10 p-2.5"
                  placeholder="Masukkan password anda"
                  @input="clearError"
                />
                <span v-if="submitted && !password" class="absolute mt-1 text-red-500 mb-2">Masukkan password anda</span>
              </div>
            </div>
              <!-- Checkbox untuk fitur "Ingat saya" -->
            <div class="flex items-center mb-4">
              <input
                v-model="rememberMe"
                type="checkbox"
                id="remember-me"
                class="h-4 w-4 text-primary_main focus:ring-primary_main border-gray-300 rounded"
              />
              <label for="remember-me" class="ml-2 block text-sm text-neutral_90">
                Ingat saya
              </label>
            </div>

            <div :class="{'mt-large': submitted && (!username || !password), 'mt-standard': !(submitted && (!username || !password))}">
              <button
                type="submit"
                class="flex w-full justify-center rounded-lg bg-primary_main px-4 py-2 text-lg font-medium leading-6 text-neutral_10 hover:bg-primary_hover"
              >
                Login
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'admin-login',
  data() {
    return {
      username: '',
      password: '',
      rememberMe: false,
      submitted: false, // This is to indicate whether the form has been submitted. Used to show/hide validation messages.
      error: null // For displaying login errors
    }
  },
  methods: {
    login: function(){
      this.$v.$touch();
      if(this.$v.$pending || this.$v.$error) return;
    },
    async loginUser() {
      this.submitted = true; // Mark the form as submitted

      if (!this.username || !this.password) {
        return; // Do not proceed if username or password is missing
      }

      try {
        // const response = await axios.post('http://company.ditenun.com/api/v1/auth/login', {
        const response = await axios.post('http://localhost:8081/api/v1/auth/login', {
          // Request body data
          username: this.username,
          password: this.password,
          rememberMe: this.rememberMe // Kirim data "Ingat saya" ke server
        });

        if (response.data.code === 200) {
          localStorage.setItem('authenticated', true);
          localStorage.setItem('token', response.data.data.token);
          // Set cookie atau local storage untuk "Ingat saya" sesuai kebutuhan
          this.$router.push('/admin/dashboard');
        } else {
          this.error = 'Login gagal, silakan periksa kredensial Anda.';
          //Handle the Error response
        }
      } catch (error) {
        // Handle the Error Response
        this.error = 'Login gagal, username atau password salah.';
        console.error('Error:', error.response.data);
      }
    },
    clearError() {
      this.error = null; // Clear any error message when user starts to correct their input
    }
  }
};
</script>

<style>
.text-red-500 {
  color: #cc5965;
}
/* Tambahkan gaya baru di bawah ini */
.error-message {
  position: absolute;
  color: #cc5965;
  margin-top: 1rem; /* Atur jarak dari input ke pesan error */
  margin-bottom: 3cm; /* Atur jarak tambahan dari pesan error ke tombol */
}

.login-button {
  display: flex;
  width: 100%;
  justify-content: center;
  border-radius: 0.375rem; /* 6px */
  background-color: #2563eb; /* Warna primer */
  padding: 0.5rem 1rem; /* 8px 16px */
  font-size: 1.125rem; /* 18px */
  font-weight: 600; /* Bold */
  color: #ffffff; /* Warna teks */
  margin-top: 3rem; /* Tambahkan margin atas */
}
</style>
