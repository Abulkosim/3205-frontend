<template>
  <div
      class="bg-gray-800 border-gray-700 w-full max-w-md border rounded-md transition-all duration-300 tracking-wide
 shadow p-6">
    <form @submit.prevent="onSubmit" class="space-y-6">
      <h5 class="text-2xl text-center font-semibold text-gray-300">Search users</h5>
      <div>
        <label for="email" class="block mb-1.5 text-base font-medium text-gray-300">Email
          <span class="text-red-600 select-none">*</span></label>
        <input v-model="email" type="email" name="email" id="email" autocomplete="off"
               class="h-11 border-2 border-transparent text-base transition-all duration-300 rounded-md outline-none focus:ring-blue-500 focus:border-gray-500 block w-full p-2.5 pl-3 bg-gray-600 text-gray-300"
               :class="{ 'border-red-500 text-red-500 focus:border-red-500 focus:text-red-500': emailError }"
               placeholder="Enter email address"
               required
               @blur="validateEmail"/>
        <div class="h-1 mt-1">
          <p v-if="emailError" class="text-sm text-red-500">{{ emailError }}</p>
        </div>
      </div>
      <div>
        <label for="number" class="block mb-1.5 text-base font-medium text-gray-300">Number</label>
        <input v-model="maskedInput" @blur="validateNumber" @input="applyMask" type="text" name="number" id="number"
               placeholder="XX-XX-XX"
               autocomplete="off"
               class="h-11 border-2 border-transparent text-base transition-all duration-300 rounded-md outline-none focus:ring-blue-500 focus:border-gray-500 block w-full p-2.5 pl-3 bg-gray-600 text-gray-300"
               :class="{ 'border-red-500 text-red-500 focus:border-red-500 focus:text-red-500':
               numberError }"
        />
        <div class="h-1 mt-1">
          <p v-if="emailError" class="text-sm text-red-500">{{ numberError }}</p>
        </div>
      </div>
      <button type="submit" :disabled="!isFormValid"
              class="flex items-center justify-center w-full h-11 text-gray-100 font-medium tracking-wide bg-blue-500 text-base hover:bg-blue-600 active:bg-blue-700 p-2.5 rounded-md outline-none transition-all duration-300">
        <span>{{ isLoading ? 'Searching...' : 'Search' }}</span>
      </button>
    </form>
  </div>
</template>

<script setup>
import {computed, ref, watch} from 'vue'
import axios from 'axios'

const email = ref('');
const rawInput = ref('');
const maskedInput = ref('');
const isLoading = ref(false);
let currentRequest = null;

const emailError = ref('');
const numberError = ref('');

const emit = defineEmits(['setLoading', 'getResults'])

const validateEmail = () => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!email.value) {
    emailError.value = 'Email is required';
  } else if (!emailRegex.test(email.value)) {
    emailError.value = 'Please enter a valid email address';
  } else {
    emailError.value = '';
  }
};

const validateNumber = () => {
  if (rawInput.value && rawInput.value.length !== 6) {
    numberError.value = 'Number must be exactly 6 digits';
  } else {
    numberError.value = '';
  }
};

const isFormValid = computed(() => {
  return !emailError.value && !numberError.value && email.value !== '';
});

const applyMask = (event) => {
  let value = event.target.value.replace(/[^0-9]/g, '');
  if (value.length > 6) value = value.slice(0, 6);

  const parts = [];
  for (let i = 0; i < value.length; i += 2) {
    parts.push(value.slice(i, i + 2));
  }

  maskedInput.value = parts.join('-');
  rawInput.value = value;
};

watch(rawInput, () => {
  applyMask({target: {value: rawInput.value}});
});

watch(email, validateEmail);
watch(rawInput, validateNumber);

const onSubmit = async () => {
  validateEmail();
  validateNumber();

  if (!isFormValid.value) return;

  if (currentRequest) {
    currentRequest.cancel();
  }

  emit('setLoading', true)
  isLoading.value = true

  const source = axios.CancelToken.source();
  currentRequest = source;

  try {
    // can also be run on localhost:3000 if you have the backend running locally
    // with the current setup, the request might take a little longer to complete
    const response = await axios.post('https://three205-backend.onrender.com/api/users/search', {
      email: email.value,
      number: rawInput.value
    }, {
      cancelToken: source.token
    })

    if (currentRequest === source) {
      emit('getResults', response.data);
    }
  } catch (error) {
    if (!axios.isCancel(error)) {
      console.error(error);
    }
  } finally {
    if (currentRequest === source) {
      isLoading.value = false;
      emit('setLoading', false);
      currentRequest = null;
    }
  }
}
</script>