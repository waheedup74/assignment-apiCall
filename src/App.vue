<script setup lang="ts">
import {ref} from 'vue';

interface FormData {
  name: string;
  email: string;
  phone: string;
  file: File | null;
  priceInterval: number;
  enableSwitch: boolean;
}

const formData = ref<FormData>({
  name: '',
  email: '',
  phone: '',
  file: null,
  priceInterval: 500,
  enableSwitch: false
});

const handleFileUpload = (event: Event) => {
  const target = event.target as HTMLInputElement;
  if (target && target.files) {
    formData.value.file = target.files[0];
  }
};

const getRoleFromToken = () => {
  let token = 'TOKEN.GAINED.BY.LOGIN';

  // Suppose it is Oauth Bearer token
  const parts = token.split('.');

  // Decode the payload (middle part)
  const decodedPayload = atob(parts[1]);

  // Parse the decoded payload as JSON
  const payloadObject = JSON.parse(decodedPayload);
  return payloadObject.role;
}

const submitForm = async () => {
  console.log(formData.value);
  
  let role = getRoleFromToken();
  // USE the role where required


  // Perform multipart API CAL
    const formDataPayload = new FormData();
    Object.keys(formData.value).forEach((key) => {
      let value: any = formData.value[key as keyof FormData];
      formDataPayload.append(key, value);      
    });
    

    try {
        const response = await fetch('http://demo_url.uk/records', {
          method: 'POST',
          body: formDataPayload,
          headers: {
            'Accept': 'application/json',
            'Content-Type': 'multipart/form-data',
          }
        });

        if (!response.ok) {
          throw new Error('Network response was not ok');
        }

        const result = await response.json();
        console.log('Success:', result);
      } catch (error) {
        console.error('Error:', error);
      }
};
</script>

<template>
<div class="h-screen">
  <div class="flex justify-center items-center h-full">
    
    <form @submit.prevent="submitForm">
      <div class="mb-4">
        <label for="name" class="text-md block text-black">Name:</label>
        <input type="text" id="name" class="border border-gray-500 rounded-md focus:outline-blue-600 w-full py-2 px-2" v-model="formData.name" required>
      </div>
      <div class="mb-4">
        <label for="email" class="text-md block text-black">Email:</label>
        <input type="email" id="email" v-model="formData.email" class="border border-gray-500 rounded-md focus:outline-blue-600 w-full py-2 px-2" required>
      </div>
      <div class="mb-4">
        <label for="phone" class="text-md block text-black">Phone:</label>
        <input type="tel" id="phone" v-model="formData.phone" class="border border-gray-500 rounded-md focus:outline-blue-600 w-full py-2 px-2" required>
      </div>
      <div class="mb-4">
        <label for="file" class="text-md block text-black">File:</label>
        <input type="file" id="file" @change="handleFileUpload">
      </div>
      <div>
        <label for="priceInterval" class="text-md block text-black">Price Interval:</label>
        <input type="range" id="priceInterval" min="0" max="1000"  v-model="formData.priceInterval">
      </div>
      <div class="flex mb-4">
        <label for="enableSwitch" class="text-md block text-black pe-4">Enable:</label>
        <input type="checkbox" id="enableSwitch" v-model="formData.enableSwitch">
      </div>
      <button type="submit" class="border border-blue-600 py-3 rounded-sm w-full">Submit</button>
    </form>
  </div>
  
  </div>
</template>

<style scoped>

</style>
