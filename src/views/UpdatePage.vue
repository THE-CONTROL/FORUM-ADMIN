<template>
  <div class="w-full text-slate-900 border-t-2">
    <div
      class="bg-slate-400 m-auto h-full rounded-md w-10/12 sm:w-6/12 pb-4 mt-5"
    >
      <h1 class="text-center text-2xl font-semibold pt-4">Update Profile</h1>
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
      <div class="w-full flex justify-center mt-4">
        <button
          class="bg-green-700 hover:bg-green-500 rounded-md p-1"
          @click="update()"
        >
          Update
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { onBeforeMount, ref } from "vue";
import { useRouter, useRoute } from "vue-router";

export default {
  name: "UpdatePage",
  props: {
    isLoggedInTrue: { type: Function },
    fetchNewToken: { type: Function },
    isLoggedIn: { type: Boolean },
    showFlash: { type: Function },
  },
  setup(props) {
    const username = ref("");
    const email = ref("");
    const route = useRoute();
    const router = useRouter();

    function getUser() {
      fetch(`https://forum-backend-cttc.onrender.com/admin/current`, {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("admin-access")}`,
        },
      })
        .then((resp) => resp.json())
        .then((data) => {
          username.value = data.admin_name;
          email.value = data.email;
        })
        .catch((error) => {
          console.log(error);
        });
    }

    function update() {
      fetch("https://forum-backend-cttc.onrender.com/admin/update", {
        method: "PUT",
        headers: {
          Authorization: `Bearer ${localStorage.getItem("admin-access")}`,
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          email: email.value.trim(),
          admin_name: username.value.trim(),
        }),
      })
        .then((resp) => resp.json())
        .then((data) => {
          props.showFlash(data.detail.msg, data.detail.success);
          if (data.detail.success) {
            router.push({
              name: "ProfilePage",
              query: {
                ...route.query,
              },
            });
          }
        })
        .catch(() => {});
    }

    onBeforeMount(() => {
      if (props.isLoggedIn) {
        getUser();
      } else {
        router.push({
          name: "LoginPage",
          query: {
            ...route.query,
          },
        });
        props.showFlash("You are not logged in!", false);
      }
    });

    return { username, update, route, router, email, getUser };
  },
};
</script>

<style></style>
