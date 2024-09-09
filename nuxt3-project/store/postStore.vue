<script>
import { defineStore } from "pinia";
import axios from "axios";


export const usePostStore = defineStore("post", {
  state: () => ({
    posts: [],
    totalPosts: 0,
    loading: false,
    page: null,
    addedArray: [],
  }),
  actions: {
    async fetchPosts(page) {
      this.page = page;
      this.loading = true;
      const response = await axios.get(
        `https://jsonplaceholder.typicode.com/posts?_page=${page}&_limit=10`
      );

      console.log("response", response);
      this.posts = response.data;

      if (!this.posts.length) {
        this.posts = this.addedArray;
      }

      this.totalPosts = parseInt(response.headers["x-total-count"], 10);
      this.loading = false;
    },

    async addPost(post) {
      post.id = this.page * 10 + 1;

      const addedPost = await fetch(
        "https://jsonplaceholder.typicode.com/posts",
        {
          method: "POST",
          body: JSON.stringify(post),
          headers: {
            "Content-type": "application/json; charset=UTF-8",
          },
        }
      )
        .then((response) => {
          if (!response.ok) {
            throw new Error("Ошибка сети");
          }
          return response.json();
        })
        .then((json) => {
          return json;
          // alert("Пост успешно добавлен");
        })
        .catch((error) => {
          console.error("Произошла ошибка:", error);
        });
      console.log("addedPost", addedPost);
      this.addedArray.push(addedPost);
    },
  },
});
</script>
