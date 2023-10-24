<template>
  <div class="w-full text-slate-900 border-t-2">
    <div
      class="bg-slate-400 m-auto h-full rounded-md w-10/12 sm:w-6/12 pb-4 mt-5"
    >
      <h1 class="text-center text-2xl font-semibold pt-4">Sign Up</h1>
      <p class="text-lg font-medium mt-4 ml-3">Username:</p>
      <div class="w-full flex justify-center">
        <input
          class="w-11/12 rounded-md bg-slate-100 focus:bg-slate-200 text-sm p-1"
          v-model="username"
          placeholder="Type in your username..."
          type="text"
        />
      </div>
      <p class="text-lg font-medium mt-4 ml-3">Email:</p>
      <div class="w-full flex justify-center">
        <input
          class="w-11/12 rounded-md bg-slate-100 focus:bg-slate-200 text-sm p-1"
          v-model="email"
          placeholder="Type in your email..."
          type="text"
        />
      </div>
      <p class="text-lg font-medium mt-2 ml-3">Password:</p>
      <div class="w-full flex justify-center">
        <input
          class="w-11/12 rounded-md bg-slate-100 focus:bg-slate-200 p-1 text-sm"
          type="password"
          v-model="password1"
          placeholder="Type in your password..."
        />
      </div>
      <p class="text-lg font-medium mt-2 ml-3">Confirm Password:</p>
      <div class="w-full flex justify-center">
        <input
          class="w-11/12 rounded-md bg-slate-100 focus:bg-slate-200 p-1 text-sm"
          type="password"
          v-model="password2"
          placeholder="Type in your password..."
        />
      </div>
      <div class="w-full flex justify-center mt-4">
        <button
          class="bg-green-700 hover:bg-green-500 rounded-md p-1"
          @click="signup()"
        >
          Sign Up
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { useRouter, useRoute } from "vue-router";

export default {
  name: "SignupPage",
  props: {
    isLoggedInTrue: { type: Function },
    showFlash: { type: Function },
  },
  setup(props) {
    const username = ref("");
    const password1 = ref("");
    const password2 = ref("");
    const email = ref("");
    const route = useRoute();
    const router = useRouter();

    async function signup() {
      fetch("https://forum-backend-cttc.onrender.com/admin/register", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          admin_name: username.value.trim(),
          email: email.value.trim(),
          password: password1.value.trim(),
          confirm_password: password2.value.trim(),
        }),
      })
        .then((resp) => resp.json())
        .then((data) => {
          if (data.detail.success) {
            router.push({
              name: "LoginPage",
              query: {
                ...route.query,
              },
            });
          }
          props.showFlash(data.detail.msg, data.detail.success);
        })
        .catch(() => {});
    }

    return { username, password1, password2, signup, route, router, email };
  },
};
</script>

<style></style>
