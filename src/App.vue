<template>
  <v-app>
    <v-app-bar app color="orange" dark>
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />
        <h2>Todo</h2>
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-main>
      <Todo />
    </v-main>
  </v-app>
</template>

<script>
import Todo from "./components/Todo";

export default {
  name: "App",

  components: {
    Todo,
  },

  data: () => ({
    //
  }),
};
</script>
