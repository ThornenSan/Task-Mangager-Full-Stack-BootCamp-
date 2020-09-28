<template>
  <div class="container">
    <h1 class="text-primary">Task Manager</h1>
    <div class="create-post">
      <input
        type="text"
        id="create-post"
        v-model="text"
        placeholder="Create a task..."
      />
      <p class="text-danger" v-if="alert">Plase Enter a task</p>
      <button v-on:click="createPost" class="btn">Submit</button>
    </div>
    <hr />
    <div class="spinner-border text-primary" role="status" v-if="loading">
      <span class="sr-only">Loading...</span>
    </div>
    <p class="error" v-if="error">{{ error }}</p>
    <div class="posts-container">
      <div
        class="post bg-warning"
        v-for="(post, index) in posts"
        v-bind:item="post"
        v-bind:index="index"
        v-bind:key="post._id"
        v-on:dblclick="deletePost(post._id)"
      >
        {{
          `${post.createdAt.getDate()}/${post.createdAt.getMonth()}/${post.createdAt.getFullYear()}`
        }}
        <p class="text text-secondary">{{ post.text }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import PostService from "../PostService";
export default {
  name: "PostComponent",
  data() {
    return {
      posts: [],
      error: "",
      text: "",
      alert: false,
      loading: false,
    };
  },
  async created() {
    try {
      this.posts = await PostService.getPosts();
    } catch (err) {
      this.error = err.message;
    }
  },
  methods: {
    async createPost() {
      if (this.text.length > 0) {
        this.alert = false;
        this.loading = true;
        await PostService.insertPost(this.text);
        this.posts = await PostService.getPosts();
        this.loading = false;
      } else {
        this.alert = true;
        this.loading = false;
      }
    },
    async deletePost(id) {
      this.loading = true;
      await PostService.deletePost(id);
      this.posts = await PostService.getPosts();
      this.loading = false;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
:root {
  --box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);
}

p.error {
  border: 1px solid #ff5b5f;
  background-color: #ffc5c1;
  padding: 10px;
  margin-bottom: 15px;
}

div.post {
  /* border: 1px solid #5bd658; */
  /* background-color: #51e2f5; */
  border-right: 5px solid #2ecc71;
  border-radius: 5px;
  box-shadow: var(--box-shadow);
  color: #333;
  display: flex;
  justify-content: space-between;
  position: relative;
  padding: 10px;
  margin: 10px 0;

  padding: 10px 10px 30px 10px;
  margin-bottom: 15px;
  transition: opacity 0.3s ease;
  opacity: 1;
}
div.post:hover {
  opacity: 0.7;
  cursor: pointer;
}
input {
  border: 1px solid #dedede;
  border-radius: 2px;
  display: block;
  font-size: 16px;
  padding: 10px;
  width: 100%;
}
.btn {
  cursor: pointer;
  background-color: #9c88ff;
  box-shadow: var(--box-shadow);
  color: #fff;
  border: 0;
  display: block;
  font-size: 16px;
  margin: 10px 0 30px;
  padding: 10px;
  width: 100%;
}

p.text {
  font-size: 22px;
  font-weight: 700;
  margin-bottom: 0;
}
</style>
