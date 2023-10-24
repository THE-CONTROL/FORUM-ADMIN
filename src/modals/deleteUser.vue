<template>
  <div class="w-full text-slate-900">
    <div
      class="bg-slate-300 m-auto h-full rounded-md w-8/12 md:w-7/12 pb-4 rounded-tr-md"
    >
      <button
        class="bg-red-700 hover:bg-red-500 float-right font-semibold text-slate-100 p-1 rounded-tr-md"
        @click="showModalFalse2()"
      >
        X
      </button>
      <h1 class="text-center text-2xl font-semibold pt-4">Delete Account</h1>
      <p class="text-center mt-4 px-2">
        Are you sure you want to delete Your Account?
      </p>
      <div class="w-full mt-2">
        <div class="w-full sm:w-8/12 sm:m-auto md:w-6/12 lg:w-4/12 xl:w-2/12">
          <div
            class="w-10/12 flex flex-wrap p-0.5 sm:p-1.5 bg-slate-400 m-auto sm:w-full"
          >
            <img
              :src="userDet.profile_picture"
              :alt="userDet.username"
              class="sm:w-14 w-6 rounded-full object-cover"
            />
            <div class="pt-0.5 overflow-auto">
              <h5 class="font-medium text-xs sm:text-lg">{{ userDet.name }}</h5>
              <h6 class="text-xs sm:text-base">{{ userDet.username }}</h6>
            </div>
          </div>
        </div>
      </div>
      <div class="w-full mt-4 flex flex-wrap justify-center">
        <button
          class="bg-green-700 hover:bg-green-500 p-1 ml-4 text-slate-100 rounded-md text-sm"
          @click="deleteUserFunc()"
        >
          Yes
        </button>
        <button
          class="bg-red-700 hover:bg-red-500 p-1 ml-4 text-slate-100 rounded-md text-sm"
          @click="showModalFalse2()"
        >
          No
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
    showModalFalse2: { type: Function },
    fetchNewToken: { type: Function },
    isLoggedIn: { type: Boolean },
    showFlash: { type: Function },
    deleteUser: { type: Number },
  },
  setup(props) {
    const route = useRoute();
    const router = useRouter();
    const userDet = ref({});

    function getUser() {
      fetch(`https://forum-backend-cttc.onrender.com/user/${props.deleteUser}`, {
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
        .catch((error) => {
          console.log(error);
        });
    }

    function deleteUserFunc() {
      fetch(`https://forum-backend-cttc.onrender.com/admin/user/delete/${props.deleteUser}`, {
        method: "DELETE",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("admin-access")}`,
        },
      })
        .then(() => {
          props.showModalFalse2();
          props.showFlash("User Deleted", true);
          router.push({
            name: "MainPage",
            query: {
              ...route.query,
            },
          });
        })
        .catch((error) => {
          console.log(error);
        });
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

    return { deleteUserFunc, route, router, userDet, getUser };
  },
};
</script>

<style></style>
