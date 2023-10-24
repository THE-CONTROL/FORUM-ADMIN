<template>
  <div class="bg-slate-100 text-slate-900">
    <div
      class="w-11/12 sm:w-10/12 md:w-9/12 lg:w-8/12 xl:w-7/12 m-auto flex flex-wrap border-x-2"
    >
      <div class="w-full">
        <h1 class="text-center text-2xl font-semibold pt-4">All Posts</h1>
        <div v-if="posts?.length">
          <div
            v-for="(post, index) in posts"
            :key="index"
            class="w-full pb-2 mt-5 bg-slate-300"
          >
            <router-link
              :to="{ name: 'OthersProfile', params: { id: post.user_id } }"
            >
              <div
                class="w-full flex flex-wrap bg-slate-500 hover:bg-slate-600 p-1.5"
              >
                <img
                  :src="post.user_pic"
                  alt="Profile Picture"
                  class="w-14 h-14 rounded-full mx-2"
                />
                <div class="h-14 pt-0.5 overflow-auto">
                  <h5 class="font-medium">{{ post.user_name }}</h5>
                  <h6>{{ post.username }}</h6>
                </div>
              </div>
            </router-link>
            <router-link
              :to="{ name: 'ContentPage', params: { contentId: post.id } }"
            >
              <div class="bg-slate-300 hover:bg-slate-400">
                <div class="w-full px-5 overflow-auto mt-2">
                  <h6>{{ post.heading }}</h6>
                </div>
                <img
                  v-if="post.image"
                  :src="post.image"
                  :alt="post.id"
                  class="w-7/12 m-auto sm:w-80 mt-2"
                />
                <div class="w-full text-right px-5 overflow-auto mt-2 text-xs">
                  <h6>Date Created: {{ post.date_created }}</h6>
                </div>
              </div>
            </router-link>
            <div class="w-full flex justify-center">
              <button
                class="bg-red-700 hover:bg-red-500 text-xs p-1 rounded-xl text-slate-100 mt-1"
                @click="showModalTrue1(post.id)"
              >
                Delete
              </button>
            </div>
          </div>
        </div>
        <div v-else>
          <h2 class="text-center text-lg font-medium pt-4">No posts yet...</h2>
        </div>
      </div>
      <div
        class="w-full bg-slate-400 py-1 mt-5 flex justify-start overflow-auto pl-2"
        v-if="meta?.pages?.length && posts?.length"
      >
        <button
          class="bg-slate-100 mr-2 border border-slate-900 rounded-md hover:bg-slate-300 px-1"
          v-if="meta?.prev_page"
          @click="getPosts(meta?.prev_page)"
        >
          prev
        </button>
        <button
          class="bg-slate-100 mr-2 border border-slate-900 rounded-md hover:bg-slate-300 px-1 text-sm"
          v-for="(page, index) in meta?.pages"
          :key="index"
          :class="page == meta?.page ? 'bg-slate-300' : ''"
          @click="getPosts(page)"
        >
          {{ page }}
        </button>
        <button
          class="bg-slate-100 mr-2 border border-slate-900 rounded-md hover:bg-slate-300 px-1"
          v-if="meta?.next"
          @click="getPosts(meta?.next_page)"
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
  name: "DeletePage",
  props: {
    contentId: { type: Number },
    showModalTrue1: { type: Function },
    showModalTrue2: { type: Function },
    fetchNewToken: { type: Function },
    isLoggedIn: { type: Boolean },
    showFlash: { type: Function },
  },
  setup(props) {
    const posts = ref([]);
    const route = useRoute();
    const router = useRouter();
    const meta = ref({});

    function getPosts(page = 1) {
      fetch(`https://forum-backend-cttc.onrender.com/post/all/${page}`, {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("admin-access")}`,
        },
      })
        .then((resp) => resp.json())
        .then((data) => {
          posts.value = data.items;
          meta.value = data.meta;
          if (page) {
            window.scrollTo(0, 0);
          }
        })
        .catch(() => {});
    }

    onBeforeMount(() => {
      if (props.isLoggedIn) {
        getPosts();
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

    return { posts, getPosts, route, router, meta };
  },
};
</script>

<style></style>
