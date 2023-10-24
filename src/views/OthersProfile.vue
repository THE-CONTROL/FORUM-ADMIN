<template>
  <div
    class="w-11/12 sm:w-10/12 md:w-9/12 lg:w-8/12 xl:w-7/12 m-auto text-slate-900 relative"
  >
    <h1 class="text-center text-2xl font-semibold pt-4">
      {{ userDet.name }}'s Profile
    </h1>
    <div class="w-full mt-5">
      <img
        :src="userDet.cover_photo"
        :alt="userDet.name"
        class="w-full h-60 bg-green-900 object-cover"
      />
      <img
        :src="userDet.profile_picture"
        :alt="userDet.name"
        class="w-40 h-40 m-auto rounded-full bg-red-900 -mt-20 object-cover"
      />
    </div>
    <h2 class="text-center sm:text-xl font-medium mt-3">{{ userDet.name }}</h2>
    <h2 class="text-center sm:text-lg mt-3">{{ userDet.username }}</h2>
    <p class="text-center sm:text-lg mt-3">Email: {{ userDet.email }}</p>
    <p class="text-center mt-3" v-if="userDet.about">
      About: {{ userDet.about }}
    </p>
    <p class="text-center mt-3">Gender: {{ userDet.sex }}</p>
    <p class="text-center mt-3" v-if="userDet.pronouns">
      Pronouns: {{ userDet.pronouns }}
    </p>
    <p class="text-center mt-3 text-xs">
      Date Joined: {{ userDet.date_joined }}
    </p>
    <div class="w-full flex flex-nowrap justify-center mt-7 text-white">
      <button class="bg-slate-900 p-1 mr-4 hover:bg-slate-700 rounded-md">
        <router-link
          :to="{ name: 'OthersPosts', params: { id: id, name: userDet.name } }"
        >
          Posts</router-link
        >
      </button>
      <button class="bg-slate-900 p-1 mr-4 hover:bg-slate-700 rounded-md">
        <router-link
          :to="{
            name: 'OthersComments',
            params: { id: id, name: userDet.name },
          }"
        >
          Comments</router-link
        >
      </button>
      <button
        class="bg-red-700 hover:bg-red-500 text-xs p-1 rounded-xl text-slate-100"
        @click="showModalTrue2(userDet.username)"
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
  name: "OthersProfile",
  props: {
    id: { type: String, required: true },
    fetchNewToken: { type: Function },
    showModalTrue3: { type: Function },
    isLoggedIn: { type: Boolean },
    showModalTrue2: { type: Function },
    showFlash: { type: Function },
  },
  setup(props) {
    const userDet = ref({ name: "User" });
    const route = useRoute();
    const router = useRouter();

    function getUser() {
      fetch(`https://forum-backend-cttc.onrender.com/user/${props.id}`, {
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
