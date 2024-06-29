<script setup>
import axios from "axios";
import { ref } from "vue";
import { vMaska } from "maska/vue";

const data = ref([]);
const user = ref({});
const errMessage = ref("");
const errText = ref("");
const loader = ref(false);

const emailRegex = /^[\w\.-]+@[\w\.-]+\.[a-zA-Z]{2,}$/;

function validateEmail() {
  return emailRegex.test(user.value.email);
}

const submit = () => {
  if (user.value.email) {
    if (validateEmail()) {
      loader.value = true;
      setTimeout(() => {
        axios
          .post("http://localhost:3000/api/user", {
            email: user.value.email,
            number: user.value?.number?.split("-").join(""),
          })
          .then((res) => {
            loader.value = false;
            if (res.data.message) {
              errMessage.value = res.data.message;
              data.value = [];
              return;
            }
            if (res.status == 200) {
              data.value = [...res.data];
              errText.value = "";
              errMessage.value = "";
            }
          })
          .catch((error) => {
            console.error(error);
          });
      }, 5000);
    } else {
      errText.value = "email manzilni kiriting";
    }
  } else {
    errText.value = "maydonni to'ldiring";
  }
};
</script>

<template>
  <!-- form -->
  <div class="max-w-[800px] border m-auto mt-12 pt-2 pb-5 rounded-xl shadow-lg">
    <div class="mb-2 text-2xl text-center">Beta version</div>
    <form @submit.prevent="" class="max-w-[400px] m-auto">
      <div>
        <div>
          <label for="email">Email</label>
        </div>
        <div class="pt-1">
          <input
            type="text"
            v-model="user.email"
            id="email"
            placeholder="test@gmail.com"
            autocomplete="off"
            class="w-full px-1 py-1 rounded-md outline-none border border-gray-500 focus:border-blue-500"
          />
          <div class="text-red-400">{{ errText }}</div>
        </div>
      </div>
      <div class="mt-6">
        <div>
          <label for="number">Number</label>
        </div>
        <div class="pt-1">
          <input
            id="number"
            v-model="user.number"
            v-maska="'##-##-##-##-##-##-##-##'"
            placeholder="12-43-53"
            autocomplete="off"
            class="w-full px-1 py-1 rounded-md outline-none border border-gray-500 focus:border-blue-500"
          />
        </div>
      </div>
      <div class="mt-4 flex justify-end">
        <button
          @click="submit()"
          class="px-3 py-2 rounded-md bg-black text-white hover:bg-black/80 active:translate-y-1 transition-all"
        >
          Qidiuv
        </button>
      </div>
    </form>
  </div>

  <!-- data -->
  <div
    class="max-w-[1000px] relative m-auto mt-10 p-10 pt-5 border rounded-xl shadow-xl"
  >
    <div
      v-if="loader"
      class="w-full h-full left-0 top-0 z-10 flex justify-center items-center absolute bg-white/60 backdrop-blur-sm"
    >
      <svg
        class="animate-spin"
        xmlns="http://www.w3.org/2000/svg"
        width="40"
        height="40"
        viewBox="0 0 24 24"
        style="fill: rgba(0, 0, 0, 1)"
      >
        <path
          d="M12 22c5.421 0 10-4.579 10-10h-2c0 4.337-3.663 8-8 8s-8-3.663-8-8c0-4.336 3.663-8 8-8V2C6.579 2 2 6.58 2 12c0 5.421 4.579 10 10 10z"
        ></path>
      </svg>
    </div>
    <div class="text-xl font-medium text-center">Ma'lumotlar</div>
    <div>
      <div v-if="data[0]" class="mt-5 flex justify-center flex-col">
        <div v-for="item of data">
          <span>Email: {{ item.email }}</span> |
          <span>Number: {{ item.number }}</span>
        </div>
      </div>
      <div
        v-if="errMessage"
        class="mt-10 text-center -rotate-12 text-3xl font-semibold text-black/30"
      >
        {{ errMessage }}
      </div>
    </div>
  </div>
</template>

<style scoped></style>
