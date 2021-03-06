<template>
  <div>
    <div class="web-header d-flex flex-column flex-sm-row sticky-top
                align-items-center px-sm-4 px-3 pt-3 border-bottom shadow"
         :class="{'header--collapse': !scrollTop}">
      <div class="d-flex align-items-center mr-sm-auto">
      <svg class="logo-pic py-0" viewBox="-200 -40 650 650">
        <logo-pic/>
      </svg>
      <h3 class="ml-3" data-e2e="title">Pensieve Log</h3>
      </div>
      <nav class="nav-indication col-2 mr-5 d-none d-lg-inline-block">
        <router-link tag="h5" to="/" class="text-center border-left border-right" style="cursor: pointer" v-if="this.isLoginPage">Login</router-link>
        <router-link tag="h5" to="/" class="text-center border-left border-right" style="cursor: pointer" v-if="this.isSignupPage">Sign-up</router-link>
      </nav>
      <div class="col-1 d-none d-lg-inline-block"></div>
      <nav class="nav-selection col-12 col-sm-4 d-flex mr-md-4 d-lg-none mb-2 my-sm-0 " @click="scrollToBottom" v-if="!isHomePage && scrollTop">
        <div class="border-left"/>
        <router-link tag="h5" to="/login" class="nav-header flex-grow-1 text-center pt-3">
          Log-in
        </router-link>
        <div class="border-left"/>
        <router-link tag="h5" to="/signup" class="nav-header flex-grow-1 text-center pt-3">
          Sign-up
        </router-link>
        <div class="border-left"/>
      </nav>
      <nav class="nav-selection col-12 col-sm-4 d-flex mr-md-4 mb-2 my-sm-0" v-if="isHomePage && scrollTop">
        <div class="border-left"/>
        <router-link tag="h5" to="/user" class="nav-header flex-grow-1 text-center pt-3" v-if="!isUserPage">
          User
        </router-link>
        <router-link tag="h5" to="/post/1" class="nav-header flex-grow-1 text-center pt-3" v-if="isUserPage">
          Home
        </router-link>
        <div class="border-left"/>
        <h5 to="/" class="nav-header flex-grow-1 text-center pt-3" @click="logout">
          Logout
        </h5>
        <div class="border-left"/>
      </nav>
    </div>
    <div class="web-body">
      <!--Pop-up Notice-->
      <notifications group="notice-app"
                     :width="500"
                     animation-name="v-fade-left"
                     position="center left">
        <template slot="body" slot-scope="props">
          <div class="custom-container"
               :class="[ props.item.type === 'error' ? 'notice-error-container' : 'notice-success-container' ]">
            <div class="custom-icon"
                 :class="[ props.item.type === 'error' ? 'notice-error-icon' : 'notice-success-icon' ]">
              <b-icon-check-circle v-if="props.item.type === 'success'"/>
              <b-icon-alert-triangle v-if="props.item.type === 'error'"/>
            </div>
            <div class="custom-template-content">
              <div :class="[ props.item.type === 'error' ? 'notice-error-title' : 'notice-success-title' ]">
                {{props.item.title}}
              </div>
              <div class="custom-template-text">
                {{props.item.text}}
              </div>
            </div>
          </div>
        </template>
      </notifications>
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
import { BIconAlertTriangle, BIconCheckCircle } from 'bootstrap-vue'
import logoPic from './assets/logo-pic.svg'
import { mapGetters } from 'vuex'
import homeMixin from './mixins/homeMixin'

export default {
  data () {
    return {
      currentPage: this.$route.path,
      lastPosition: null,
      lastPositionAlter: null,
      scrollThreshold: 70
    }
  },
  computed: {
    ...mapGetters([
      'getCurrentPathName',
      'isFrontPage',
      'isLoginPage',
      'isSignupPage',
      'isUserPage',
      'scrollTop'
    ]),
    isHomePage () {
      return (!this.isFrontPage && !this.isLoginPage && !this.isSignupPage)
    }
  },
  mixins: [
    homeMixin
  ],
  components: {
    logoPic,
    // eslint-disable-next-line vue/no-unused-components
    BIconAlertTriangle,
    // eslint-disable-next-line vue/no-unused-components
    BIconCheckCircle
  },
  created () {
    this.$store.dispatch('setCurrentPathName', this.$route.name)
    window.addEventListener('scroll', this.scrollHandler, true)
  },
  watch: {
    $route (to, from) {
      this.$store.dispatch('setCurrentPathName', this.$route.name)
    }
  },
  methods: {
    scrollToBottom () {
      const container = document.querySelector('html')
      const scrollHeight = container.scrollHeight
      container.scrollTop = scrollHeight
    },
    logout () {
      this.mixinLogout()
    },
    scrollHandler () {
      if (window.scrollY - this.lastPosition > this.scrollThreshold) {
        this.$store.dispatch('setScrollTop', false)
        this.lastPosition = window.scrollY
      } else if (this.lastPosition - window.scrollY > this.scrollThreshold) {
        this.$store.dispatch('setScrollTop', true)
        this.lastPosition = window.scrollY
      }
    }
  }
}
</script>
<style>
  * {
    border: 0;
    margin: 0;
    box-sizing: border-box;
    font-family: 'Ubuntu', sans-serif;
  }
  .web-header{
    background-color: rgba(255, 255, 255, 0.95);
    background-image: linear-gradient(to right , rgba(138, 126, 112, 0.12), #fff);
  }
  .logo-pic{
    height: 8vh;
  }
  .form-login{
    height: 90vh;
    padding-top: 15vh;
  }
  .form-signup{
    height: 90vh;
    padding-top: 25vh;
  }
  .button {
    font-weight: 700;
    cursor: pointer;
    border-radius: 100px;
    box-shadow: 0px 3px 10px rgba(0, 0, 0, 0.2);
    transition: color 1s ease, background-color .5s ease;
  }
  .button-login:hover,
  .button-login:active,
  .button-signup:hover,
  .button-signup:active {
    border: 6px solid #004662;
    background-color: #fcfff2;
    color: #004662;
  }
  .button-confirm:hover,
  .button-confirm:active{
    border: 3px solid #004662;
    background-color: #fcfff2;
    color: #004662;
  }
  .button-reset:hover,
  .button-reset:active{
    border: 3px solid #E32A3A;
    background-color: rgba(251, 222, 225, 0.5) ;
    color: #E32A3A;
  }
  .button-disabled{
    color: rgba(119, 119, 119, 0.6);
    cursor: default;
  }
  .button-action :hover{
    transition: all .2s;
    transform: translateY(-3px);
  }
  .button-action :active{
    transition: all .1s;
    transform: translateY(-1px);
    box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.3);
  }

  .nav-header{
    cursor: pointer;
  }
  .nav-header::after{
    display: block;
    content: '';
    padding-bottom: 4px;
    border-bottom: 2px solid #004662;
    transform: scaleX(0);
    transition: transform 250ms ease-in-out;
  }
  .nav-header:hover::after {
    transform: scaleX(0.6);
  }
  .page-redir{
    cursor: pointer;
    font-weight: 500;
    font-size: 110%;
    color: #004662;
    border-bottom: 3px solid #004662;
  }
  .page-redir:hover{
    color: rgba(0, 70, 98, 0.67);
    border-bottom: 3px solid rgba(0, 70, 98, 0.67);
  }

  /* Apply to xs-size */
  @media screen and (max-width: 500px) {
    .custom-container {
      transform: translateX(-70px) scale(0.7);
    }
  }
  /* Apply to ms-size */
  @media screen and (max-width: 575px) {
    .header--collapse{
      animation: collapse 200ms ease-in;
      animation-fill-mode: forwards;
      -webkit-animation: collapse 200ms ease-in;
      -webkit-animation-fill-mode: forwards
    }
    @keyframes collapse {
      0% {height: 18vh}
      100% {height: 10vh}
    }
    @-webkit-keyframes collapse {
      0% {height: 18vh}
      100% {height: 10vh}
    }
    .icon-upshift {
      animation: shift 200ms ease-in;
      animation-fill-mode: forwards;
      -webkit-animation: shift 200ms ease-in;
      -webkit-animation-fill-mode: forwards;
    }
    @keyframes shift {
      0% {transform: translateY(0);}
      100% {transform: translateY(-20px);}
    }
    @-webkit-keyframes shift {
      0% {transform: translateY(0);}
      100% {transform: translateY(-20px);}
    }
  }
  @media screen and (max-width: 768px){
    .line-top{
      font-size: 3.5rem;
    }
    .line-bottom{
      font-size: 2.5rem;
    }
    .custom-template{
      transform: translateX(-50px) scale(0.8);
    }
  }
  /* Apply to md-size or below */
  @media screen and (max-width: 992px){
    .form-login{
      height: 90vh;
      padding-top: 20vh;
    }
    .form-signup{
      height: 90vh;
      padding-top: 15vh;
    }
    .form-border{
      border-top: 3px groove #004662;
    }
  }
  /*<-------- Page Transition ------->  */
  .app-transition-enter-active {
    animation: app-enter 1.20s ease-out;
    animation-fill-mode: forwards;
  }

  .app-transition-leave-active {
    animation: app-leave 0.65s ease-in;
  }

  @keyframes app-leave {
    0%{ transform: translateX(0); opacity: 100%}
    30%{ transform: translateX(+5%); opacity: 100%}
    40%{ transform: translateX(+5%); opacity: 100%}
    75%{ transform: translateX(0); opacity: 40%}
    90%{ transform: translateX(-20%); opacity: 10%}
    100%{ transform: translateX(-40%); opacity: 0%}
  }
  @keyframes app-enter {
    0% { transform: translateX(+40%); opacity: 0%}
    10% { transform: translateX(+20%); opacity: 10%}
    25% { transform: translateX(0); opacity: 40%}
    60% { transform: translateX(-10%); opacity: 100%}
    100% { transform: translateX(0); opacity: 100%}
  }

  /*  ==========================  */
  /*      Notification Style      */
  /*  ==========================  */
  .custom-container {
    display: flex;
    font-size: 12px;
    margin: 10px 5px 0px 5px;
    align-items: center;
    border-radius: 20px;
  }
  .notice-success-container{
    background: #E8F9F0;
    border: 2px solid #1abc9c;
  }
  .notice-error-container {
    background: #fad7d3;
    border: 2px solid #e74c3c;
  }
  .custom-icon {
    flex: 0 1 10%;
    font-size: 45px;
    font-weight: 600;
    padding: 0 10px !important;
  }
  .notice-success-icon{
    color: #15C371;
  }
  .notice-error-icon {
    color: #f29f97;
  }
  .notice-success-title{
    font-size: 1.4rem;
    font-weight: 600;
    color: #1abc9c;
  }
  .notice-error-title{
    font-size: 1.4rem;
    font-weight: 600;
    color: #e37a73;
  }
  .custom-template-text {
    font-size: 0.95rem;
    font-weight: 400;
    letter-spacing: +0.5px;
  }

  .v-fade-left-enter-active,
  .v-fade-left-leave-active,
  .v-fade-left-move {
    transition: all .5s;
  }
  .v-fade-left-enter,
  .v-fade-left-leave-to {
    opacity: 0;
    transform: translateX(-500px) scale(0.2);
  }
</style>
