<template>
  <div class="d-flex flex-wrap justify-content-around" style="height: 90vh; overflow: scroll">
    <div class="col-6 post-container" v-for="post in getPosts">
      <div class="post-header d-flex">
        <p class="ml-3 mr-1 mb-0">
          <span>#</span>
          {{post.id}}
        </p>
        <h4 class="ml-1 mr-auto">{{post.username}}</h4>

        <div class="post-ctrl-icons mr-5" v-if="getUserData.username === post.username">
          <BIconCheckCircle class="icon-check" v-if="editPostId === post.id" @click="openModal(post.id, 'edit')"/>
          <BIconTerminalFill class="icon-terminal" v-else @click="clickEdit(post.id,post.postText)" />
          <BIconXCircle class="icon-x ml-2" v-if="editPostId === post.id" @click="editPostId=0"/>
          <BIconXCircleFill class="icon-xcirclefill ml-2" v-else @click="openModal(post.id, 'delete')" />
        </div>
      </div>
      <div class="post-divider col-9 mx-auto"/>
      <div class="post-time d-flex justify-content-end">
        <p class="mr-5 mb-2">{{ post.creationTimestamp | moment("from", "now", true)}} ago</p>
      </div>

      <div class="post-body post-body-edit mx-3" v-if="editPostId === post.id">
        <b-form-textarea class="pl-3" rows="3" max-rows="5" v-model="editPostText"/>
      </div>
      <div class="post-body post-body-display mx-3" v-else>
        <b-form-textarea class="pl-3" ows="3" max-rows="5" plaintext :value="post.postText"/>
      </div>
      <div class="post-divider col-10 mx-auto my-3"/>
      <div class="d-flex">
        <div class="post-like-button flex-grow-1 text-center ml-5">
          <post-like :postData="post"/>
        </div>
        <div style="border-right: 2px solid rgba(158,158,158,0.4)"/>
        <div class="post-comment-button flex-grow-1 text-center mr-5">
          <p class="m-auto font-weight-bolder">{{post.postComments.length}} Comment<span v-if="post.postComments.length>1">s</span></p>
        </div>
      </div>
      <post-comment :postData="post"/>
    </div>
    <delete-confirm :modal-message="'You are going to delete this post'" :modal-action="deletePost"/>
    <edit-confirm :modal-message="'You are going to make change to the post'" :modal-action="editPost"/>
  </div>
</template>
<script>
  import axios from "axios";
  import {mapGetters, mapActions} from 'vuex';
  import homeMixin from "../mixins/homeMixin";

  import postLike from '../components/postLike';
  import postComment from '../components/postComment'
  import editModal from "../components/modal/editModal";
  import deleteModal from "../components/modal/deleteModal";

  import {BIconClockFill, BIconPersonFill, BIconXCircleFill,
    BIconTerminalFill, BIconXCircle, BIconCheckCircle} from 'bootstrap-vue';

  export default {
    data(){
      return{
        currentPage: this.$route.params.page,

        editPostId: 0,
        editPostText: ''
      }
    },
    computed: {
      ...mapGetters([
        "getUserData",
        "getPosts"
      ])
    },
    mixins:[
      homeMixin
    ],
    created() {
      this.mixinGetPosts(this.currentPage - 1);
    },
    watch:{
      $route(to,from){
        this.mixinGetPosts(to.params.page -1);
      }
    },
    methods:{
      editPost(id){
        this.mixinEditPost(id, this.editPostText);
      },
      deletePost(id) {
        this.mixinDeletePost(id);
      },
      clickEdit(postId, postText){
        this.editPostId=postId;
        this.editPostText=postText
      },
      openModal(id, mode){
        this.$store.dispatch('setModal',{id: id, mode: mode});
        this.$store.dispatch('modalOn');
      },
    },
    components:{
      postLike: postLike,
      postComment: postComment,
      deleteConfirm :deleteModal,
      editConfirm: editModal,
      BIconClockFill,
      BIconPersonFill,
      BIconXCircleFill,
      BIconTerminalFill,
      BIconXCircle,
      BIconCheckCircle,
    }
  }
</script>
<style>
  .post-container{
    border-radius: 50px;
    box-shadow: 0px 3px 10px rgba(0, 0, 0, 0.2);
    padding-top: 30px;
    padding-bottom: 10px;
  }
  .post-divider{
    border-bottom: 2px solid rgba(158,158,158,0.4)
  }
  .post-header>span{
    font-size: 1.1em
  }
  .post-header>p{
    font-size: 80%;
    font-weight: 600;
    letter-spacing: -1px;
    color: rgba(0,70,98,0.73);
    transform: translateY(-5px)
  }

  .post-time{
    font-size: 80%;
    font-weight: 600;
    color: rgba(0,70,98,0.5);
  }

  .post-ctrl-icons {
    font-size: 1.3rem;
  }

  .icon-terminal:hover{
    transition: all .2s;
    transform: scale(1.5);
    color: #6184a8;
  }
  .icon-xcirclefill:hover{
    transition: all .2s;
    transform: scale(1.5);
    color: rgba(231, 76, 60, 0.88);
  }

  .icon-check{
    transform: scale(1.4);
    color: #88f900;
  }
  .icon-x{
    transform: scale(1.4);
    color: #dc0ebc;
  }

  .icon-check:hover,
  .icon-x:hover{
    transition: all .2s;
    transform: scale(1.5);
  }

  .icon-terminal:active,
  .icon-xcirclefill:active,
  .icon-check:active,
  .icon-x:active{
    transform: translateY(-1px);
    transition: all .2s;
  }

  .post-body {
    padding: 3px 20px;
    border-width: 0px 5px;
    border-style: solid;
    border-color: #adb7bf;
    border-radius: 10px ;
  }
  .post-body>textarea{
    font-size: 120% ;
    border-radius: 10px;
    background: rgba(235, 235, 235, 0.30);
  }
  .post-body>textarea:focus{
    outline: 0;
    box-shadow: none;
  }

  .post-body-edit{
    border-radius: 10px;
    box-shadow: 0 0 20px 5px rgba(192, 202, 210, 0.36);
  }
  .post-body-edit>textarea{
    border: white;
    box-shadow: none;
    padding: 6px 0;
    background: white;
  }
  .post-like-button{
    height: 30px;
    background-image: radial-gradient(ellipse at top right ,#ffffff, #f5f5f5);
    border-radius: 10px;
    box-shadow: 0 9px 15px 5px #ebebeb;
  }
  .post-comment-button{
    height: 30px;
    background-image: radial-gradient(ellipse at top left ,#ffffff, #f5f5f5);
    border-radius: 10px;
    box-shadow: 0 9px 15px 5px #ebebeb;
  }
</style>