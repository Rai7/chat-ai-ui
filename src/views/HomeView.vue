<script setup lang="ts">
import { ref } from 'vue';
import axios from 'axios';
import { useUserStore } from '../stores/user';
import { useRouter } from 'vue-router';

// Importing assets (background and robot image)
import backgroundImage from '../assets/background-image.png';
import robotImage from '../assets/robot.png';

const userStore = useUserStore();
const router = useRouter();

const name = ref('');
const email = ref('');
const loading = ref(false);
const error = ref('');

const createUser = async () => {
  if (!name.value || !email.value) {
    error.value = 'Name and email are required';
    return;
  }

  loading.value = true;
  error.value = '';

  try {
    const { data } = await axios.post(`${import.meta.env.VITE_API_URL}/register-user`, {
      name: name.value,
      email: email.value,
    });

    userStore.setUser({
      userId: data.userId,
      name: data.name,
    });

    router.push('/chat');
  } catch (err) {
    error.value = 'Something went wrong. Please try again';
  } finally {
    loading.value = false;
  }
};
</script>

<template>
  <!-- Outer container with background image from assets -->
  <div
    class="h-screen w-full flex items-center justify-center text-white bg-cover bg-center bg-no-repeat"
    :style="{ backgroundImage: `url(${backgroundImage})` }"
  >
    <!-- Form container with semi-transparent background for readability -->
    <div class="relative z-10 p-8 bg-gray-800/90 rounded-lg shadow-lg w-full max-w-md">
      <img :src="robotImage" alt="Robot Icon" class="mx-auto w-24 h-24 mb-4" />
      <h1 class="text-2xl font-semibold mb-4 text-center">Welcome To Ramones Works GPT</h1>

      <input
        type="text"
        class="w-full p-2 mb-2 bg-gray-700 text-white rounded-lg focus:outline-none"
        placeholder="Name"
        v-model="name"
      />
      <input
        type="email"
        class="w-full p-2 mb-2 bg-gray-700 text-white rounded-lg focus:outline-none"
        placeholder="Email"
        v-model="email"
      />
      <button
        @click="createUser"
        class="w-full p-2 bg-blue-500 rounded-lg disabled:opacity-50 transition"
        :disabled="loading"
      >
        {{ loading ? 'Logging in...' : 'Start Chat' }}
      </button>

      <p v-if="error" class="text-red-400 text-center mt-2">{{ error }}</p>
    </div>
  </div>
</template>
