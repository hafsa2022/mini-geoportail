<template>
  <div class="">
    <v-footer dark padless>
      <v-card
        width="100%"
        class="text-white text-center"
        style="background-color: #0e4082"
      >
        <v-card-text class="">
          <h1 class="py-2 text-white">You Can Find Us At</h1>
          <a
            target="_blank"
            v-bind:href="social.url"
            v-for="social in socials"
            :key="social.id"
          >
            <v-btn class="ma-4" icon>
              <v-icon size="24px" style="color: #0e4082">{{
                social.icon
              }}</v-icon>
            </v-btn>
          </a>
        </v-card-text>

        <v-card-text class="text-white">
          Copyright {{ new Date().getFullYear() }}
          <span class="text-white">- Hafsa El Akhdar </span>
          | GIS Engineering
        </v-card-text>
      </v-card>
    </v-footer>

    <!-- <h5 class="resize">
        <v-icon color="black" class="left" large>mdi-chevron-double-left</v-icon
        >Resize screen to see magic<v-icon color="black" class="right" large
          >mdi-chevron-double-right</v-icon
        >
      </h5> -->
  </div>
</template>

<script>
export default {
  data: () => ({
    panel: [],
    socials: [
      {
        name: "facebook",
        icon: "mdi-facebook",
        url: "#",
      },
      {
        name: "twitter",
        icon: "mdi-twitter",
        url: "#",
      },
      { name: "google", icon: "mdi-google", url: "https://codepen.io/jettaz" },
      {
        name: "linkedin",
        icon: "mdi-linkedin",
        url: "#",
      },
      {
        name: "instagram",
        icon: "mdi-instagram",
        url: "#",
      },
    ],
  }),

  methods: {},
};
</script>

<style>
.v-footer {
  padding: 8px 0px;
  border-radius: 0px;
}
</style>
