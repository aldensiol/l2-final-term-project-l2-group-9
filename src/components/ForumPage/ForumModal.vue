<template>
    <div class="modal-overlay"  @click.self="closeForumModal">
      <div class="modal-content">
        <h2>{{ editMode ? 'Edit Forum Post' : 'Add a new Forum Post' }}</h2>
        <form @submit.prevent="submitForumPost">
          <!-- Job Comapny Input -->
          <input
            type="text"
            v-model="forumPost.title"
            placeholder="Title"
            required
            class="form-input"
          />  
          <!-- Job Details Input -->
          <textarea
            v-model="forumPost.details"
            placeholder="Add details (E.g. Interview Process of Tik Tok Backend Engineer Internship)"
            rows="4"
            class="form-textarea"
            maxlength="500"
          ></textarea>
  
          <!-- Submit Button -->
          <button type="submit" class="form-button" @click="submitForumPost">{{ editMode ? 'Edit Forum Post' : 'Add To Forum' }}</button>
        </form>
  
        <!-- Close Button -->
        <button @click="closeForumModal" class="close-button">Close</button>
      </div>
    </div>
  </template>
  
  <script>
  import { getAuth, onAuthStateChanged } from "firebase/auth";
  import { getFirestore, getDoc, doc } from "firebase/firestore";
  import firebaseApp from "@/firebase.js";

  const db = getFirestore(firebaseApp);
  
  export default {
    name: "ForumModal",
    props: {
      editMode: {
        type: Boolean,
        default: false,
      },
      post: {
            type: Object,
            default: null,
        },
    },
    data() {
      return {
      forumPost: { ...this.post } || {
      postId: "",
      name: "",
      title: "",
      details: "",
      likes: 0,
      datePosted: "",
    },
      user: null,
      };
    },
    async mounted() {
        const auth = getAuth();
        onAuthStateChanged(auth, async (user) => {
            if (user) {
                this.user = user;
                const userDoc = await getDoc(doc(db, "users", this.user.uid));
                if (userDoc.exists()) {
                    this.forumPost.name = userDoc.data().username;
                }
            }
          });
    },
    methods: {
      submitForumPost () {
        this.forumPost.datePosted = new Date();
        if (!this.editMode) {
          this.forumPost.postId = new Date().getTime().toString();
        }
        this.$emit('forumPostCreated', { ...this.forumPost });
        this.closeForumModal();
      },
  
      resetForumPost () {
        this.forumPost = {
          postId: "",
          name: this.userName,
          title: "",
          details: "",
          likes: 0,
          datePosted: "",
        };
      },
      closeForumModal () {
        this.resetForumPost();
        if (this.editMode) {
          this.$emit('close-modal-edit-mode');
          return;
        }
        this.$emit('close-modal');
      },
    },
  };
  </script>
  
  <style scoped>
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
  }
  
  .modal-content {
    background: #fff;
    padding: 2rem;
    border-radius: 16px;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
    width: 90%;
    max-width: 55%;
  }
  
  h2 {
    font-size: 1.5rem;
    color: #333;
    margin-bottom: 1.5rem;
  }
  
  .form-input,
  .form-select,
  .form-textarea {
    width: 100%;
    padding: 0.8rem;
    border-radius: 8px;
    border: 1px solid #ccc;
    margin-bottom: 1rem;
    font-size: 1rem;
    font-family: "Poppins", sans-serif;
  }
  
  .form-button {
    background-color: #4caf50;
    color: white;
    border: none;
    padding: 0.8rem 1.6rem;
    font-size: 1rem;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  
  .form-button:hover {
    background-color: #45a049;
  }
  
  .close-button {
    background-color: #f44336;
    color: white;
    border: none;
    padding: 0.8rem 1.6rem;
    font-size: 1rem;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    margin-top: 1rem;
  }
  
  .close-button:hover {
    background-color: #d32f2f;
  }
  </style>
  