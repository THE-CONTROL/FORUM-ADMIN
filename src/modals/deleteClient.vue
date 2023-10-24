<template>
  <div class="w-full text-slate-900">
    <div
      class="bg-slate-300 m-auto h-full rounded-md w-8/12 md:w-7/12 pb-4 rounded-tr-md"
    >
      <button
        class="bg-red-700 hover:bg-red-500 float-right font-semibold text-slate-100 p-1 rounded-tr-md"
        @click="showModalFalse3()"
      >
        <router-link to="/profile">X</router-link>
      </button>
      <h1 class="text-center text-2xl font-semibold pt-4">Delete Account</h1>
      <p class="text-center mt-4 px-2">
        Are you sure you want to delete Your Account?
      </p>
      <div class="w-full mt-2">
        <div class="w-full sm:w-8/12 sm:m-auto md:w-6/12 lg:w-4/12 xl:w-2/12">
          <div
            class="w-10/12 flex flex-wrap p-1.5 bg-slate-400 m-auto sm:w-full pb-3"
          >
            <div class="text-center py-2 w-full">
              <h5 class="text-xl font-medium">{{ userDet.admin_name }}</h5>
              <h5>{{ userDet.email }}</h5>
            </div>
          </div>
        </div>
      </div>
      <div class="w-full mt-4 flex flex-wrap justify-center">
        <button
          class="bg-green-700 hover:bg-green-500 p-1 ml-4 text-slate-100 rounded-md text-sm"
          @click="deleteUser()"
        >
          Yes
        </button>
        <button
          class="bg-red-700 hover:bg-red-500 p-1 ml-4 text-slate-100 rounded-md text-sm"
          @click="showModalFalse3()"
        >
          <router-link to="/profile">No</router-link>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { onBeforeMount, ref } from "@vue/runtime-core";
import { useRouter, useRoute } from "vue-router";

export default {
  name: "DeleteClient",
  props: {
    showModalFalse3: { type: Function },
    fetchNewToken: { type: Function },
    logoutFunc: { type: Function },
    isLoggedIn: { type: Boolean },
    showFlash: { type: Function },
    isLoggedInFalse: { type: Function },
  },
  setup(props) {
    const route = useRoute();
    const router = useRouter();
    const userDet = ref({});

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

    function deleteUser() {
      fetch(`https://forum-backend-cttc.onrender.com/admin/delete`, {
        method: "DELETE",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("admin-access")}`,
        },
      })
        .then((resp) => resp.json())
        .then((data) => {
          if (data.detail.success) {
            props.logoutFunc("Account Deleted!");
            props.showModalFalse3();
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

    return { deleteUser, route, router, userDet, getUser };
  },
};
</script>

<style></style>
