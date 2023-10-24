<template>
  <div
    v-if="!showModal1 && !showModal2 && !showModal3"
    class="bg-slate-100 text-slate-100 pb-16 min-h-screen"
    :class="flash ? 'pt-20' : 'pt-12'"
  >
    <nav
      class="bg-slate-900 w-full flex flex-wrap sm:justify-between justify-start fixed top-0 z-10"
    >
      <button @click="showNavFunc()" class="sm:hidden mx-2">
        <fa :icon="['fas', 'navicon']" />
      </button>
      <h1 class="text-3xl w-fit py-2 ml-3 text-blue-900 font-bold">
        POSEIDON<span class="text-xs ml-2 text-green-100 font-bold">Admin</span>
      </h1>
      <div class="hidden sm:flex sm:flex-wrap py-1">
        <h3 class="w-fit py-3 px-2 h-full hover:bg-blue-600" v-if="isLoggedIn">
          <router-link to="/">All Users</router-link>
        </h3>
        <h3 class="w-fit py-3 px-2 h-full hover:bg-blue-600" v-if="isLoggedIn">
          <router-link to="/profile">Profile</router-link>
        </h3>
        <h3 class="w-fit py-3 px-2 h-full hover:bg-blue-600" v-if="isLoggedIn">
          <router-link to="/posts">All Posts</router-link>
        </h3>
        <h3 class="w-fit py-3 px-2 h-full hover:bg-blue-600" v-if="isLoggedIn">
          <router-link to="/comments">All Comments</router-link>
        </h3>
        <h3
          class="w-fit py-3 px-2 h-full hover:bg-blue-600"
          v-if="isLoggedIn"
          @click="logoutFunc('Logout successful!')"
        >
          Logout
        </h3>
        <h3 class="w-fit py-3 px-2 h-full hover:bg-blue-600" v-if="!isLoggedIn">
          <router-link to="/login">Login</router-link>
        </h3>
        <h3 class="w-fit py-3 px-2 h-full hover:bg-blue-600" v-if="!isLoggedIn">
          <router-link to="/signup">SignUp</router-link>
        </h3>
      </div>
      <div
        class="w-full flex flex-nowrap justify-between pl-5 py-1"
        v-if="flash"
        :class="success ? 'bg-green-400' : 'bg-red-400'"
      >
        <p>{{ flash }}</p>
        <button
          class="h-6 bg-red-900 hover:bg-red-700 mr-3 rounded-md px-1"
          @click="removeFlash()"
        >
          Dismiss
        </button>
      </div>
    </nav>
    <nav class="bg-slate-800 sm:hidden" :class="showNav ? 'block' : 'hidden'">
      <h3
        class="w-full py-3 px-2 h-fit hover:bg-blue-600 text-center"
        @click="showNavFunc()"
        v-if="isLoggedIn"
      >
        <router-link to="/"> All Users</router-link>
      </h3>
      <h3
        class="w-full py-3 px-2 h-fit hover:bg-blue-600 text-center"
        v-if="isLoggedIn"
        @click="showNavFunc()"
      >
        <router-link to="/profile"> Profile</router-link>
      </h3>
      <h3
        class="w-full py-3 px-2 h-fit hover:bg-blue-600 text-center"
        v-if="isLoggedIn"
        @click="showNavFunc()"
      >
        <router-link to="/posts"> All Posts</router-link>
      </h3>
      <h3
        class="w-full py-3 px-2 h-fit hover:bg-blue-600 text-center"
        v-if="isLoggedIn"
        @click="showNavFunc()"
      >
        <router-link to="/comments"> All Comments</router-link>
      </h3>
      <h3
        class="w-full py-3 px-2 h-fit hover:bg-blue-600 text-center"
        v-if="isLoggedIn"
        @click="logoutFunc('Logout successful!'), showNavFunc()"
      >
        Logout
      </h3>
      <h3
        class="w-full py-3 px-2 h-fit hover:bg-blue-600 text-center"
        v-if="!isLoggedIn"
        @click="showNavFunc()"
      >
        <router-link to="/login"> Login</router-link>
      </h3>
      <h3
        class="w-full py-3 px-2 h-fit hover:bg-blue-600 text-center"
        v-if="!isLoggedIn"
        @click="showNavFunc()"
      >
        <router-link to="/signup"> Sign up</router-link>
      </h3>
    </nav>
    <router-view
      :showModalTrue1="showModalTrue1"
      :isLoggedInTrue="isLoggedInTrue"
      :fetchNewToken="fetchNewToken"
      :showModalTrue3="showModalTrue3"
      :isLoggedIn="isLoggedIn"
      :showFlash="showFlash"
      :showModalTrue2="showModalTrue2"
    />
    <div class="w-full bg-slate-700 text-right pr-3 py-1 fixed bottom-0 left-0">
      <h1 class="font-bold text-sm"><i>Created by:</i> CONTROL</h1>
    </div>
  </div>
  <div v-if="showModal1" class="w-screen min-h-screen bg-slate-100 pt-20 pb-20">
    <DeletePost
      :showModalFalse1="showModalFalse1"
      :postId="postId"
      :fetchNewToken="fetchNewToken"
      :isLoggedIn="isLoggedIn"
      :showFlash="showFlash"
    />
  </div>
  <div v-if="showModal2" class="w-screen min-h-screen bg-slate-100 pt-20 pb-20">
    <DeleteUser
      :showModalFalse2="showModalFalse2"
      :fetchNewToken="fetchNewToken"
      :isLoggedIn="isLoggedIn"
      :showFlash="showFlash"
      :deleteUser="deleteUser"
    />
  </div>
  <div v-if="showModal3" class="w-screen min-h-screen bg-slate-100 pt-20 pb-20">
    <DeleteClient
      :showModalFalse3="showModalFalse3"
      :fetchNewToken="fetchNewToken"
      :logoutFunc="logoutFunc"
      :isLoggedIn="isLoggedIn"
      :showFlash="showFlash"
      :isLoggedInFalse="isLoggedInFalse"
    />
  </div>
</template>

<script>
import { onBeforeMount, onBeforeUnmount, ref } from "vue";
import DeletePost from "../modals/deletePost.vue";
import DeleteClient from "../modals/deleteClient.vue";
import DeleteUser from "../modals/deleteUser.vue";
import { useRouter, useRoute } from "vue-router";

export default {
  name: "HomeWorld",
  components: {
    DeletePost,
    DeleteClient,
    DeleteUser,
  },
  setup() {
    const showNav = ref(false);
    const showModal1 = ref(false);
    const showModal2 = ref(false);
    const showModal3 = ref(false);
    const postId = ref(null);
    const isLoggedIn = ref(false);
    const route = useRoute();
    const router = useRouter();
    const flash = ref("");
    const success = ref(true);
    const deleteUser = ref("");
    const interval = ref("");

    function showNavFunc() {
      showNav.value = !showNav.value;
      window.scrollTo(0, 0);
    }

    function showModalFalse1() {
      showModal1.value = false;
      postId.value = null;
    }

    function showModalTrue1(index) {
      postId.value = index;
      showModal1.value = true;
    }

    function showModalFalse2() {
      showModal2.value = false;
      deleteUser.value = "";
    }

    function showModalTrue2(id) {
      showModal2.value = true;
      deleteUser.value = id;
    }

    function showModalFalse3() {
      showModal3.value = false;
    }

    function showModalTrue3() {
      showModal3.value = true;
    }

    function isLoggedInTrue() {
      isLoggedIn.value = true;
    }

    function isLoggedInFalse() {
      isLoggedIn.value = false;
    }

    function logoutFunc(value) {
      localStorage.clear("admin-refresh");
      localStorage.clear("admin-access");
      isLoggedInFalse();
      router.push({
        name: "LoginPage",
        query: {
          ...route.query,
        },
      });
      showFlash(value, true);
    }

    function fetchNewToken() {
      if (isLoggedIn.value) {
        fetch("https://forum-backend-cttc.onrender.com/admin/refresh", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            Authorization: `Bearer ${localStorage.getItem("refresh-admin")}`,
          },
        })
          .then((resp) => resp.json())
          .then((data) => {
            if (data.access_token) {
              localStorage.setItem("admin-access", data.access_token);
            } else {
              logoutFunc();
            }
          })
          .catch((error) => {
            console.log(error);
          });
      }
    }

    function removeFlash() {
      flash.value = "";
    }

    function showFlash(message, bool) {
      (flash.value = message), (success.value = bool);
    }

    onBeforeMount(() => {
      if (localStorage.getItem("refresh-admin")) {
        isLoggedInTrue();
        fetchNewToken();
      }
      interval.value = setInterval(fetchNewToken, 240000);
    });

    onBeforeUnmount(() => {
      clearInterval(interval.value);
    });

    return {
      showNav,
      showNavFunc,
      showModalFalse1,
      showModalTrue1,
      showModal1,
      showModalFalse2,
      showModalTrue2,
      showModal2,
      postId,
      isLoggedIn,
      isLoggedInTrue,
      logoutFunc,
      route,
      router,
      isLoggedInFalse,
      fetchNewToken,
      showModal3,
      showModalFalse3,
      showModalTrue3,
      flash,
      removeFlash,
      success,
      showFlash,
      deleteUser,
      interval,
    };
  },
};
</script>

<style></style>
