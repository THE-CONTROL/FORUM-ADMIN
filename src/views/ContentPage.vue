<template>
  <div
    class="w-11/12 sm:w-10/12 md:w-9/12 lg:w-8/12 xl:w-7/12 m-auto h-full px-5 pt-3 pb-6 text-center text-slate-900"
  >
    <router-link
      :to="{ name: 'OthersProfile', params: { id: postDetails?.user_id } }"
    >
      <div class="w-full flex flex-wrap p-1.5 bg-slate-500 hover:bg-slate-600">
        <img
          :src="postDetails?.user_pic"
          :alt="postDetails?.username"
          class="w-14 h-14 rounded-full mx-2"
        />
        <div class="h-14">
          <h5 class="font-medium">{{ postDetails?.user_name }}</h5>
          <h6 class="text-left">{{ postDetails?.username }}</h6>
        </div>
      </div>
    </router-link>
    <h1 class="text-2xl font-semibold">{{ postDetails?.heading }}</h1>
    <img
      :src="postDetails?.image"
      :alt="postDetails?.id"
      v-if="postDetails?.image"
      class="sm:w-6/12 w-60 m-auto mt-3"
    />
    <p class="mt-3">{{ postDetails?.content }}</p>
    <p class="mt-3 text-right">Date created: {{ postDetails?.date_created }}</p>
    <div class="w-full mt-5">
      <h2 class="text-2xl font-medium">Comments</h2>
      <div v-if="comments?.length">
        <div v-for="(comment, index) in comments" :key="index" class="w-full">
          <div class="w-full mt-2 pb-1 bg-slate-300 rounded-md text-sm">
            <router-link
              :to="{ name: 'OthersProfile', params: { id: comment.user_id } }"
            >
              <div
                class="w-full flex flex-wrap justify-start bg-slate-500 hover:bg-slate-600 p-1.5"
              >
                <img
                  :src="comment.user_pic"
                  :alt="comment.username"
                  class="w-10 h-10 rounded-full mx-2"
                />
                <div class="pt-0.5 overflow-auto">
                  <h5 class="font-medium">{{ comment.user_name }}</h5>
                  <h6 class="text-left">{{ comment.username }}</h6>
                </div>
              </div>
            </router-link>
            <p class="my-4 mx-5 text-left">{{ comment.content }}</p>
            <p class="text-right text-xs pr-3">
              Date created: {{ comment.date_created }}
            </p>
            <div
              class="w-full flex justify-center text-xs text-slate-100 mt-0.5"
            >
              <button
                class="bg-red-700 p-0.5 rounded-sm hover:bg-red-500"
                @click="deleteComment(comment.post_id, comment.id)"
              >
                Delete
              </button>
            </div>
          </div>
        </div>
      </div>
      <div v-else>
        <h3 class="text-base font-medium">No comments yet...</h3>
      </div>
      <div
        class="w-full bg-slate-400 py-1 mt-5 flex justify-start overflow-auto pl-2"
        v-if="meta?.pages?.length"
      >
        <button
          class="bg-slate-100 mr-2 border border-slate-900 rounded-md hover:bg-slate-300 px-1"
          v-if="meta?.prev_next"
          @click="getComments(meta?.prev_page)"
        >
          prev
        </button>
        <button
          class="bg-slate-100 mr-2 border border-slate-900 rounded-md hover:bg-slate-300 px-1 text-xs"
          v-for="(page, index) in meta?.pages"
          :key="index"
          :class="page == meta?.page ? 'bg-slate-300' : ''"
          @click="getComments(page)"
        >
          {{ page }}
        </button>
        <button
          class="bg-slate-100 mr-2 border border-slate-900 rounded-md hover:bg-slate-300 px-1"
          v-if="meta?.next_next"
          @click="getComments(meta?.next_page)"
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
  name: "ContentPage",
  props: {
    contentId: { type: [Number, String], required: true },
    fetchNewToken: { type: Function },
    isLoggedIn: { type: Boolean },
    showFlash: { type: Function },
  },
  setup(props) {
    const comments = ref([]);
    const postDetails = ref({ user_id: "0" });
    const route = useRoute();
    const router = useRouter();
    const meta = ref({});

    function getPostDetails() {
      fetch(`https://forum-backend-cttc.onrender.com/post/${props.contentId}`, {
        method: "GET",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${localStorage.getItem("admin-access")}`,
        },
      })
        .then((resp) => resp.json())
        .then((data) => {
          postDetails.value = data;
          getComments();
        })
        .catch(() => {});
    }

    function getComments(page = 1) {
      fetch(
        `https://forum-backend-cttc.onrender.com/post/comments/all/${props.contentId}/${page}`,
        {
          method: "GET",
          headers: {
            "Content-Type": "application/json",
            Authorization: `Bearer ${localStorage.getItem("admin-access")}`,
          },
        }
      )
        .then((resp) => resp.json())
        .then((data) => {
          comments.value = data.items;
          meta.value = data.meta;
          if (page) {
            window.scrollTo(0, 0);
          }
        })
        .catch(() => {});
    }

    function deleteComment(postId, commentId) {
      fetch(
        `https://forum-backend-cttc.onrender.com/admin/comment/delete/${postId}/${commentId}`,
        {
          method: "DELETE",
          headers: {
            "Content-Type": "application/json",
            Authorization: `Bearer ${localStorage.getItem("admin-access")}`,
          },
        }
      )
        .then(() => {
          props.showFlash("Comment Deleted!", true);
          getComments();
        })
        .catch((error) => {
          console.log(error);
        });
    }

    onBeforeMount(() => {
      if (props.isLoggedIn) {
        getPostDetails();
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

    return {
      comments,
      getPostDetails,
      postDetails,
      getComments,
      route,
      router,
      deleteComment,
      meta,
    };
  },
};
</script>

<style></style>
