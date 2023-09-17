<template>
  <v-app-bar
    style="background-color: #fff"
    class="navbar-text"
    app
    flat
    height="120"
  >
    <v-toolbar-title class="logo"
      ><span style="font-size: 35px; font-weight: 600">
        <v-img :src="srcImg" controls width="300" height="150" />
      </span>
    </v-toolbar-title>
    <v-spacer></v-spacer>
    <div class="mr-4">
      <v-img
        src="../assets/GeoportailLogo.png"
        alt=""
        controls
        width="300"
        height="150"
      />
    </div>
  </v-app-bar>
  <NavDrawer />
</template>

<script>
import NavDrawer from "../components/NavDrawer.vue";
export default {
  name: "NavBar",
  data: () => ({
    srcImg: require("../assets/CRSALogo.png"),
  }),
  components: {
    NavDrawer,
  },
};
</script>

<style>
/* .logo {
  top: 45px;
  width: 100%;
} */
@media (max-width: 1200px) {
  .v-img {
    height: 150px;
    width: 100px;
  }
}
</style>
