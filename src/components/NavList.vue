<template>
  <div>
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
          prepend-icon="mdi-folder"
          title="My Files"
          value="myfiles"
        ></v-list-item>
        <v-list-item
          prepend-icon="mdi-account-multiple"
          title="Shared with me"
          value="shared"
        ></v-list-item>
        <v-list-item
          prepend-icon="mdi-star"
          title="Starred"
          value="starred"
        ></v-list-item>
      </v-list>
    </v-card>
  </div>
</template>

<script>
export default {
  name: "NavList",
  data: () => ({
    help: { text: "FAQ", icon: "mdi-help-circle-outline", link: "/help" },
  }),
};
</script>
