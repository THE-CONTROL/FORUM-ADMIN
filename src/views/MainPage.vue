<template>
  <div class="bg-slate-100 text-slate-900">
    <div
      class="w-11/12 sm:w-10/12 md:w-9/12 lg:w-8/12 xl:w-7/12 m-auto flex flex-wrap border-x-2"
    >
      <div class="w-full">
        <h1 class="text-center text-2xl font-semibold pt-4">All Users</h1>
        <div v-if="users?.length">
          <div
            v-for="(user, index) in users"
            :key="index"
            class="w-full pb-2 mt-5 bg-slate-300"
          >
            <router-link
              :to="{ name: 'OthersProfile', params: { id: user.id } }"
            >
              <div
                class="w-full flex flex-wrap p-1.5 bg-slate-500 hover:bg-slate-600"
              >
                <img
                  :src="user.profile_picture"
                  :alt="user.username"
                  class="w-14 h-14 rounded-full mx-2"
                />
                <div class="h-14 pt-0.5 overflow-auto">
                  <h5 class="font-medium">{{ user.name }}</h5>
                  <h6>{{ user.username }}</h6>
                </div>
              </div>
            </router-link>
            <div class="w-full flex justify-center">
              <button
                class="bg-red-700 hover:bg-red-500 text-xs p-1 rounded-xl text-slate-100 mt-2"
                @click="showModalTrue2(user.id)"
              >
                Delete
              </button>
            </div>
          </div>
        </div>
        <div v-else>
          <h2 class="text-center text-lg font-medium pt-4">No users yet...</h2>
        </div>
      </div>
      <div
        class="w-full bg-slate-400 py-1 mt-5 flex justify-start overflow-auto pl-2"
        v-if="meta?.pages?.length && users?.length"
      >
        <button
          class="bg-slate-100 mr-2 border border-slate-900 rounded-md hover:bg-slate-300 px-1"
          v-if="meta?.prev_page"
          @click="getUsers(meta?.prev_page)"
        >
          prev
        </button>
        <button
          class="bg-slate-100 mr-2 border border-slate-900 rounded-md hover:bg-slate-300 px-1 text-sm"
          v-for="(page, index) in meta?.pages"
          :key="index"
          :class="page == meta?.page ? 'bg-slate-300' : ''"
          @click="getUsers(page)"
        >
          {{ page }}
        </button>
        <button
          class="bg-slate-100 mr-2 border border-slate-900 rounded-md hover:bg-slate-300 px-1"
          v-if="meta.next_page"
          @click="getUsers(meta?.next_page)"
        >
          next
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { onBeforeMount, ref } from "vue";
import { useRouter, useRoute } from "vue-router";

export default {
  name: "MainPage",
  props: {
    showModalTrue2: { type: Function },
    showFlash: { type: Function },
    isLoggedIn: { type: Boolean },
    fetchNewToken: { type: Function },
  },
  setup(props) {
    const users = ref([]);
    const meta = ref({});
    const route = useRoute();
    const router = useRouter();

    function getUsers(page = 1) {
      fetch(`https://forum-backend-cttc.onrender.com/user/all/${page}`, {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("admin-access")}`,
        },
      })
        .then((resp) => resp.json())
        .then((data) => {
          users.value = data.items;
          meta.value = data.meta;
          if (page) {
            window.scrollTo(0, 0);
          }
        })
        .catch((error) => {
          console.log(error);
        });
    }

    onBeforeMount(() => {
      if (props.isLoggedIn) {
        getUsers();
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

    return { users, getUsers, meta, route, router };
  },
};
</script>

<style></style>
