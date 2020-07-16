<template>
  <div id="app">
    <Header :isHeader="isHeader" />
    <router-view @submit-login-data="login" />
  </div>
</template>

<script src="https://use.fontawesome.com/releases/v5.2.0/js/all.js"></script>
<script>
import "./assets/css/style.scss";
import Header from "./components/common/Header.vue";
import constants from "./lib/constants";
import axios from "axios";

const SERVER_URL = "http://127.0.0.1:8080";
export default {
  name: "App",
  components: {
    Header
  },
  created() {
    let url = this.$route.name;

    this.checkUrl(url);
  },
  watch: {
    $route(to) {
      this.checkUrl(to.name);
    }
  },
  methods: {
    setCookie(token) {
      this.$cookies.set("auth-token", token);
      this.isLoggedIn = true;
    },
    checkUrl(url) {
      let array = [constants.URL_TYPE.USER.LOGIN, constants.URL_TYPE.USER.JOIN];

      let isHeader = true;
      array.map(path => {
        if (url === path) isHeader = false;
      });
      this.isHeader = isHeader;
    },
    login(loginData) {
      axios
        .post(SERVER_URL + "/account/login", loginData)
        .then(res => {
          console.log(res);
          this.$router.push({ name: "constants.URL_TYPE.POST.MAIN" });
        })
        .catch(err => console.log(err.response.data));
    },
    logout() {
      const config = {
        heders: {
          Authorization: `Token ${this.$cookies.get("auth-token")}`
        }
      };
      axios
        .post(SERVER_URL + "/rest-auth/logout/", null, config)
        .then(() => {
          this.isLoggedIn = false;
          this.$router.push({ name: "constants.URL_TYPE.POST.MAIN" });
        })
        .catch(err => console.log(err.response.data));
    }
  },
  data: function() {
    return {
      isHeader: true,
      constants,
      isLoggedIn: false,
      errorMessages: null
    };
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
