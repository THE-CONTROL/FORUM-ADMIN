<template>
  <div class="w-full text-slate-900 border-t-2">
    <div
      class="bg-slate-400 m-auto h-full rounded-md w-10/12 sm:w-6/12 pb-4 mt-5"
    >
      <h1 class="text-center text-2xl font-semibold pt-4">Login</h1>
      <p class="text-lg font-medium mt-4 ml-3">Email:</p>
      <div class="w-full flex justify-center">
        <input
          class="w-11/12 rounded-md bg-slate-100 focus:bg-slate-200 t ext-sm p-1"
          placeholder="Type in your email..."
          type="email"
          v-model="email"
        />
      </div>
      <p class="text-lg font-medium mt-2 ml-3">Password:</p>
      <div class="w-full flex justify-center">
        <input
          class="w-11/12 rounded-md bg-slate-100 focus:bg-slate-200 p-1 text-sm"
          type="password"
          v-model="password"
          placeholder="Type in your password..."
        />
      </div>
      <div class="w-full flex justify-center mt-4">
        <button
          class="bg-green-700 hover:bg-green-500 rounded-md p-1"
          @click="login()"
        >
          Login
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { useRouter, useRoute } from "vue-router";

export default {
  name: "LoginPage",
  props: {
    isLoggedInTrue: { type: Function },
    showFlash: { type: Function },
  },
  setup(props) {
    const email = ref("");
    const password = ref("");
    const route = useRoute();
    const router = useRouter();

    function login() {
      fetch("https://forum-backend-cttc.onrender.com/admin/login", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          email: email.value.trim(),
          password: password.value.trim(),
        }),
      })
        .then((resp) => resp.json())
        .then((data) => {
          if (data.detail.success) {
            localStorage.setItem("admin-access", data.detail.access);
            localStorage.setItem("refresh-admin", data.detail.refresh);
            props.isLoggedInTrue();
            router.push({
              name: "ProfilePage",
              query: {
                ...route.query,
              },
            });
          }
          props.showFlash(data.detail.msg, data.detail.success);
        })
        .catch((error) => {
          console.log(error);
        });
    }

    return { email, password, login, route, router };
  },
};
</script>

<style></style>
