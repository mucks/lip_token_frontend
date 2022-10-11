<template>
  <div v-if="lipDetails" class="lip-container">
    <img :src="parts.bg[lipDetails.bg]" class="lip-style" />
    <img :src="parts.mask[lipDetails.mask]" class="lip-style" />
    <img :src="parts.line[lipDetails.line]" class="lip-style" />
    <img :src="parts.addon[lipDetails.addon]" class="lip-style" />
    <img :src="parts.addonMouth1[lipDetails.addonMouth1]" class="lip-style" />
    <img :src="parts.addonMouth2[lipDetails.addonMouth2]" class="lip-style" />
    <img :src="parts.addonMouth3[lipDetails.addonMouth3]" class="lip-style" />
  </div>
</template>

<style scoped>
.lip-style {
  width: 100%;
  height: 100%;
  position: absolute;
}
.lip-container {
  min-width: 200px;
  min-height: 200px;
  background: blue;
  position: relative;
}
</style>

<script lang="ts">
import { parts } from "../js/parts";
import Vue from "vue";
export default Vue.extend({
  props: ["lip"],
  data: () => ({
    lipDetails: null as any,
    parts: {} as any,
  }),
  mounted() {
    this.parts = parts;

    if (!this.$props.lip) return;
    let dnaStr = String(this.$props.lip.dna);

    //while (dnaStr.length < 16) dnaStr = "0" + dnaStr;

    console.log(dnaStr);

    dnaStr = dnaStr.replace("0x", "");

    const lipDetails = {
      bg: parseInt(dnaStr.substring(0, 2), 16) % 5,
      mask: parseInt(dnaStr.substring(2, 4), 16) % 5,
      line: parseInt(dnaStr.substring(4, 6), 16) % 5,
      addon: parseInt(dnaStr.substring(6, 8), 16) % 5,
      addonMouth1: parseInt(dnaStr.substring(8, 10), 16) % 5,
      addonMouth2: parseInt(dnaStr.substring(10, 12), 16) % 5,
      addonMouth3: parseInt(dnaStr.substring(12, 14), 16) % 5,
      name: this.$props.lip.name,
    };

    console.log(lipDetails);
    this.lipDetails = lipDetails;
  },
});
</script>

<style>
</style>