<template>
  <div class="home mt-3">
    <div class="container">
      <div v-for="post in posts" :key="post.id">
        <div class="card shadow p-2 mb-4 bg-body rounded">
          <div class="card-body">
            <router-link
              class="navbar-brand"
              :to="{ name: 'blogPost', params: { blogPostID: post.id } }"
            >
              <h1 class="mb-1 post-title">{{ post.title }}</h1>
            </router-link>
            <p class="mb-0">
              Posted by: <span class="post-author"> {{ post.author }}</span>
            </p>

            <p class="mb-0">Posted at: {{ post.created_at }}</p>
          </div>
        </div>
      </div>
      <div class="my-4">
        <p v-show="this.loadingblogPosts">...loading...</p>
        <button
          v-show="next"
          @click="getBlogPosts"
          class="btn btn-sm btn-outline-success"
        >
          Load More
        </button>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";

export default {
  name: "HomeView",
  data() {
    return {
      posts: [],
      next: null,
      loadingblogPosts: false,
    };
  },

  methods: {

    // Function to fetch the data from the API
    // In a bigger project, we should make a helpers folder and
    // make all the http requests there.

    async getBlogPosts() {
      let endpoint = "/api/blog-posts/";
      if (this.next) {
        endpoint = this.next;
      }

      this.loadingblogPosts = true;
      try {
        const response = await axios.get(endpoint);
        this.posts.push(...response.data.results);
        this.loadingblogPosts = false;

        if (response.data.next) {
          this.next = response.data.next;
        } else {
          this.next = null;
        }

        // Just to output the date in a better format.
        this.posts.map((x) => {
          const date = new Date(x.created_at);
          x.created_at = date.toDateString();
        });
        console.log(this.posts);
      } catch (error) {
        console.log(error.response);
        alert(error.response.statusText);
      }
    },
  },

  // Using the life cycle hook to retrieve data. 
  created() {
    document.title = "Skyler Blog";
    this.getBlogPosts();
  },
};
</script>

<style scoped>
.post-author {
  color: #306bac !important;
}

.post-title {
  font-weight: bold;
  font-size: 150%;
  color: #a8763e;
}
.post-title:hover {
  color: #306bac;
}
</style>
