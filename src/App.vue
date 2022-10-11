<template>
  <v-app dark>
    <v-main>
      <router-view />
    </v-main>
  </v-app>
</template>

<script lang="ts">
import Vue from "vue";

export default Vue.extend({
  name: "App",

  mounted() {
    this.$vuetify.theme.dark = true;
  },

  data: () => ({
    //
  }),
});
</script>
