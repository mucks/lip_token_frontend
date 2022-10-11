<template>
  <div>
    <substrate-connecter />
  </div>
</template>

<script lang="ts">
import SubstrateConnecter from "@/components/SubstrateConnecter.vue";
import Vue from "vue";

export default Vue.extend({
  name: "HomeView",

  components: {
    SubstrateConnecter,
  },
});
</script>
