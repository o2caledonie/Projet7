<template>
  <div id="posts-list">
    <div class="container mx-auto p-3" v-for="post in posts" :key="post.postId">
      <div class="card post border-3 mb-3 mx-auto">
        <div class="card-header d-flex">
          <Avatar
            v-if="post.owner.avatar == 'null'"
            :src="'/user-circle-solid.svg'"
            class="avatar"
          />
          <Avatar v-else :src="post.owner.avatar" class="avatar" />

          <p class="card-text mx-3">
            {{ post.owner.userName }}
          </p>

          <p class="card-text ms-auto px-3">
            <small class="text-muted">
              Publié le {{ dateFormat(post.createdAt) }}
            </small>
          </p>
        </div>
        <div class="card-body p-0">
          <p class="card-text text-start mx-3 mt-2">
            {{ post.content }}
          </p>
          <PostImage v-if="post.image == 'null'" class="post-image" />
          <PostImage
            v-else
            :src="post.image"
            class="post-image"
            alt="Image de la publication"
          />
        </div>
        <div class="row m-0 justify-content-center">
          <div class="mx-auto my-2 col-8 col-sm-4">
            <DeletePostBtn :post="post" @post-deleted="handlePostDeleted" />
          </div>
          <div class="mx-auto my-2 col-8 col-sm-4">
            <UpdatePostBtn :post="post" @post-updated="handlePostUpdated" />
          </div>
        </div>

        <Comments :post="post" />
      </div>
    </div>
  </div>
</template>

<script>
// import axios from "axios";
import moment from "moment";
import Avatar from "../components/Avatar.vue";
import PostImage from "../components/PostImage.vue";
import Comments from "../components/Comments.vue";
import DeletePostBtn from "../components/DeletePostBtn.vue";
import UpdatePostBtn from "../components/UpdatePostBtn.vue";

export default {
  name: "PostsList",
  components: {
    Avatar,
    PostImage,
    Comments,
    DeletePostBtn,
    UpdatePostBtn,
  },
  props: {
    posts: { type: Array },
  },

  data() {
    return {
      userId: localStorage.getItem("userId"),
      username: localStorage.getItem("username"),
      isAdmin: localStorage.getItem("isAdmin"),
      imageProfile: localStorage.getItem("imageProfile"),
      imagePost: "",
      imagePreview: null,
      content: "",
      comments: [],
      post: "",
    };
  },
  methods: {
    //Emit to parent component when post is deleted
    handlePostDeleted() {
      this.$emit("post-deleted");
    },

    handlePostUpdated() {
      this.$emit("post-updated");
    },

    // date and time
    dateFormat(date) {
      if (date) {
        return moment(String(date)).format("DD/MM/YYYY à hh:mm");
      }
    },
  },
};
</script>

<style lang="scss">
.post {
  max-width: 500px;
  .post-image {
    width: 100%;
    height: auto;
  }
}
</style>