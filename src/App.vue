<template>
  <div>
    <!-- <nav>
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </nav> -->
    <sidebar-menu :menu="menu" />
    <router-view />
  </div>
</template>

<script>
export default {
  name: "HomeView",
  data() {
    return {
      menu: [
        {
          header: "Main Navigation",
          hiddenOnCollapse: true,
        },
        {
          href: "/dashboard",
          title: "Dashboard",
          icon: "fa fa-user",
        },
        {
          href: "/charts",
          title: "Products",
          icon: "fa fa-chart-area",
          child: [
            {
              href: "/charts/sublink",
              title: "Sub Link",
            },
          ],
        },
      ],
      checkViewLogIn: false
    };
  },
  created(){
    console.log(this.$route.name);
    console.log(this.$router.currentRoute.path); 
  }
};
</script>

<style lang="scss">
// @import "../assets/cooladmin/vendor/font-awesome-4.7/css/font-awesome.min.css";
// @import "../assets/cooladmin/vendor/font-awesome-5/css/fontawesome-all.min.css";
// @import "../assets/cooladmin/vendor/mdi-font/css/material-design-iconic-font.min.css";
// @import "../assets/cooladmin/vendor/bootstrap-4.1/bootstrap.min.css";
// @import "../assets/cooladmin/vendor/animsition/animsition.min.css";
// @import "../assets/cooladmin/vendor/bootstrap-progressbar/bootstrap-progressbar-3.3.4.min.css";
// @import "../assets/cooladmin/vendor/wow/animate.css";
// @import "../assets/cooladmin/vendor/css-hamburgers/hamburgers.min.css";
// @import "../assets/cooladmin/vendor/slick/slick.css";
// @import "../assets/cooladmin/vendor/select2/select2.min.css";
// @import "../assets/cooladmin/vendor/perfect-scrollbar/perfect-scrollbar.css";
// @import "../assets/cooladmin/css/theme.css";
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
