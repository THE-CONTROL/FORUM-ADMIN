<template>
  <div
    class="w-11/12 sm:w-10/12 md:w-9/12 lg:w-8/12 xl:w-7/12 m-auto text-slate-900"
  >
    <h2 class="text-center text-xl font-medium mt-6">
      {{ userDet.admin_name }}
    </h2>
    <p class="text-center text-lg mt-3">Email: {{ userDet.email }}</p>
    <p class="text-center mt-3 text-xs">
      Date Joined: {{ userDet.date_joined }}
    </p>
    <div class="w-full flex flex-nowrap justify-center mt-7 text-white">
      <button class="bg-blue-900 p-1 mr-4 hover:bg-blue-700 rounded-md">
        <router-link to="/update/profile">Update</router-link>
      </button>
      <button
        class="bg-red-900 p-1 hover:bg-red-700 rounded-md"
        @click="showModalTrue3()"
      >
        Delete
      </button>
    </div>
  </div>
</template>

<script>
import { onBeforeMount, ref } from "vue";
import { useRouter, useRoute } from "vue-router";

export default {
  name: "ProfilePage",
  props: {
    fetchNewToken: { type: Function },
    showModalTrue3: { type: Function },
    isLoggedIn: { type: Boolean },
    showFlash: { type: Function },
  },
  setup(props) {
    const userDet = ref({});
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
          userDet.value = data;
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

    return { userDet, getUser, route, router };
  },
};
</script>

<style></style>
