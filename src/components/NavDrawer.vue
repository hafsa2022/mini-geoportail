<template>
  <v-card>
    <v-navigation-drawer
      v-model="drawer"
      :rail="rail"
      permanent
      @click="rail = false"
    >
      <v-card class="d-flex flex-column" flat plain>
        <v-list>
          <v-list-item prepend-icon="mdi-menu">
            <template v-slot:append>
              <v-btn
                variant="text"
                icon="mdi-chevron-left"
                @click.stop="rail = !rail"
              ></v-btn>
            </template>
          </v-list-item>
        </v-list>

        <v-divider></v-divider>

        <v-list density="compact" nav>
          <v-list-item
            :prepend-icon="home.icon"
            :title="home.text"
            :to="home.link"
          ></v-list-item>
          <v-list-item
            :prepend-icon="help.icon"
            :title="help.text"
            :to="help.link"
          ></v-list-item>
        </v-list>
      </v-card>
    </v-navigation-drawer>
  </v-card>
</template>

<script>
// import NavList from "../components/NavList.vue";
// import { useDisplay } from "vuetify";
export default {
  name: "NavDrawer",
  data: () => ({
    drawer: true,
    rail: true,
    home: { text: "Home", icon: "mdi-home", link: "/" },
    help: { text: "FAQ", icon: "mdi-help-circle-outline", link: "/help" },
  }),
  components: {
    // NavList,
  },
};
</script>

<style></style>
