  
<template>
  <div id="posts">
    <Navbar />
    <div class="card bg-light_newPost border-3 rounded my-5 mx-auto p-3">
      <div class="card-body">
        <h1 class="card-title fw-bold">Bonjour, {{ userName }} !</h1>
        <div class="user-avatar">
          <ProfileImage
            v-if="avatar == 'null'"
            :src="'/user-circle-solid.png'"
            class="newPost__photo"
          />
          <ProfileImage v-else :src="avatar" class="newPost__photo" />
        </div>

        <form @submit.prevent="createPost" aria-label="Nouveau message">

          <div class="form-floating my-3">
            <textarea
              v-model="content"
              class="form-control"
              name="message"
              id="message"
              placeholder="Leave a comment here"
              aria-label="Rédiger un nouveau message"
            />
            <label for="message">Quoi de neuf ?</label>

            <div id="preview">
              <img
                v-if="imagePreview"
                :src="imagePreview"
                class="img-fluid"
                alt="Prévisualisation de l'image ajoutée au message"
              />
            </div>
          </div>

          <div class="row justify-content-center">
         
              <button
                @click="uploadFile"
                type="button"
                class="btn btn-outline-light border-3  mx-auto mb-3 col-8 col-sm-4"
              >
                <i class="far fa-images fa-2x"></i> Choisir un fichier
              </button>

              <input
                type="file"
                ref="fileUpload"
                @change="onFileSelected"
                accept="image/*"
                aria-label="Sélectionner un fichier"
                id="file-input"
                class="d-none"
              />
           

            <button
              type="submit"
              class="btn btn-light border-2 mx-auto mb-3 col-10 col-sm-6"
              aria-label="Publier le message"
            >
              Publier <i class="far fa-paper-plane"></i>
            </button>
          </div>

        </form>
      </div>
    </div>

    <div class="my-4">
      <h2>Publications</h2>
      <PostsList :posts="posts" @post-deleted="fetchPosts" />
    </div>
    <router-view />
  </div>
</template>


<script>
import axios from "axios";
import PostsList from "../components/PostsList.vue";
import Navbar from "../components/Navbar.vue";
import ProfileImage from "../components/ProfileImage.vue";
export default {
  name: "Posts",
  components: {
    Navbar,
    ProfileImage,
    PostsList,
  },
  inject: ["notyf"],
  data() {
    return {
      userName: localStorage.getItem("userName"),
      isAdmin: localStorage.getItem("isAdmin"),
      avatar: localStorage.getItem("avatar"),
      post: "",
      image: "",
      imagePreview: null,
      content: "",
      posts: [],
    };
  },
  created() {
    this.fetchPosts();
  },
  methods: {
    //Reset form
    resetForm() {
      this.content = "";
      this.image = "";
      this.imagePreview = null;
    },

    // Create a new post
    uploadFile() {
      this.$refs.fileUpload.click();
    },
    onFileSelected(event) {
      this.image = event.target.files[0];
      this.imagePreview = URL.createObjectURL(this.image);
    },
    createPost() {
      const formData = new FormData();
      formData.append("content", this.content);
      formData.append("image", this.image);
      axios
        .post("http://localhost:3000/api/post", formData, {
          headers: {
            "Content-Type": "multipart/form-data",
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then(() => {
          this.notyf.success("Votre publication a bien été créée !");
          this.resetForm(), this.fetchPosts();
        })
        .catch((error) => {
          const msgerror = error.response.data;
          this.notyf.error(msgerror.error);
        });
    },

    //Fetch Posts from API
    fetchPosts() {
      axios
        .get("http://localhost:3000/api/post", {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then((response) => {
          this.posts = response.data;
        })
        .catch((error) => {
          const msgerror = error.response.data;
          this.notyf.error(msgerror.error);
        });
    },
  },
};
</script>


<style scoped lang="scss">
.card {
  &.bg-light {
    &_newPost {
      max-width: 500px;
      background-image: linear-gradient(#fff, #192B48);
    }
  }
}
.rounded {
  @media (min-width: 500px) {
    border-radius: 1.5rem !important;
  }
}

.form-control {
    &:focus,
        &:hover {
          border-color: #000;
          box-shadow: 0 0 0.2em 0.2em #000;
        }
}

.btn-light {
    transform: scale(1);
      transition: transform 200ms ease-in-out !important;
    &:focus,
        &:hover {
            font-weight: bold;
            color: #000;
          border-color: #fd2d01;
          box-shadow: 0 0 0.2em 0.2em #fd2d01;
          transform: scale(1.1);
        }
 
}

//   background: #ffb1b1;
//   border-radius: 25px;
//   width: 50%;
//   @media (max-width: 950px) {
//     width: 60%;
//   }
//   @media (max-width: 768px) {
//     width: 70%;
//   }
//   @media (max-width: 550px) {
//     width: 80%;
//   }
//   @media (max-width: 450px) {
//     width: 90%;
//   }
//   &__photo__image {
//     width: 47px;
//   }
//   &__content__text {
//     border-radius: 0 15px;
//     border: none;
//     margin: 1.5rem 0 0 0;
//     max-width: 50rem;
//     width: 90%;
//     min-height: 5rem;
//   }
//   &__content__image {
//     max-width: 50rem;
//     width: 90%;
//     height: 274px;
//     margin: 1rem auto;
//     object-fit: cover;
//   }
//   &__option {
//     display: flex;
//     justify-content: space-around;
//     align-items: center;
//     &__file > input {
//       display: none;
//     }
//     &__file {
//       &__btnInvisible {
//         display: flex;
//         align-items: center;
//         color: #3f3d56;
//         border: none;
//         background-color: #ffb1b1;
//         &:hover,
//         &:focus {
//           color: white;
//         }
//       }
//     }
//     &__button {
//       border: 2px solid #3f3d56;
//       border-radius: 25px;
//       color: #3f3d56;
//       font-size: 15px;
//       font-weight: bold;
//       padding: 0.4rem;
//       margin: 1rem;
//       outline-style: none;
//       &:hover,
//       &:focus {
//         color: #ff6363;
//       }
//     }
//   }
// }
</style>