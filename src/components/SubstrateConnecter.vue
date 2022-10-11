<template>
  <v-container>
    <v-row justify="center" align="center">
      <v-col md="3">
        <h1>Our Game</h1>
        <div class="my-4">
          <v-btn :disabled="api != null" @click="connect">Connect</v-btn>
        </div>
        <div class="my-4">
          <v-text-field v-model="nameInput" type="text" label="Name" />
          <v-btn
            :disabled="
              api == null || lipTokenContract == null || nameInput == ''
            "
            @click="createRandomLip"
          >
            Create Random Lip
          </v-btn>
        </div>
        <div class="my-4">
          <v-btn
            :disabled="api == null || lipTokenContract == null"
            @click="getLips"
            >Get Lips</v-btn
          >
        </div>
      </v-col>
    </v-row>
    <v-row>
      <lip-renderer
        class="mx-2 my-2"
        v-for="lip in lips"
        :key="lip.id"
        :lip="lip"
      />
      <!-- <v-col md="2" v-for="lip in lips" :key="lip.id">
        <v-card elevation="2">
          <v-card-text>
            <p>ID: {{ lip.id }}</p>
            <p>DNA {{ lip.dna }}</p>
            <p>LEVEL: {{ lip.level }}</p>
            <p>NAME: {{ lip.name }}</p>
            <p>RARITY: {{ lip.rarity }}</p>
          </v-card-text>
        </v-card>
      </v-col> -->
    </v-row>
  </v-container>
</template>

<script lang="ts">
import { ApiPromise, WsProvider } from "@polkadot/api";
import { ContractPromise } from "@polkadot/api-contract";
import LipRenderer from "./LipRenderer.vue";
import {
  web3Accounts,
  web3Enable,
  web3FromAddress,
} from "@polkadot/extension-dapp";

import metadata from "@/assets/metadata.json";

const delay = (ms: number) =>
  new Promise((resolve, _reject) => setTimeout(() => resolve(null), ms));

const SENDER = "5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY";

import Vue from "vue";
export default Vue.extend({
  data: () => ({
    api: null as ApiPromise | null,
    injector: null as any,
    lipTokenContract: null as ContractPromise | null,
    lips: [] as any[],
    nameInput: "",
  }),
  components: {
    LipRenderer,
  },
  methods: {
    async connect() {
      this.api = await this.connectToNode();
      await delay(1000);
      this.injector = await this.loginWithExtension();
      this.setupLipTokenContract();
    },

    async getLips() {
      if (!this.lipTokenContract) return;

      const resp = await this.lipTokenContract.query.getLips(SENDER, {});
      if (resp.result.isOk) {
        const lips = resp.output?.toHuman();
        this.lips = lips as any;
      }
    },

    async createRandomLip() {
      if (!this.lipTokenContract) return;
      const value = 1000000000000;
      const gasLimit = 74999922688;
      const storageDepositLimit = null;

      const resp = await this.lipTokenContract.tx
        .createRandomLip(
          { gasLimit, storageDepositLimit, value: value },
          this.nameInput
        )
        .signAndSend(SENDER, { signer: this.injector.signer });
      console.log(resp);
      await delay(2000);
      await this.getLips();
    },

    async connectToNode(): Promise<ApiPromise> {
      // Construct
      const url = "ws://127.0.0.1:9944";
      const wsProvider = new WsProvider(url);
      return ApiPromise.create({ provider: wsProvider });
    },

    async loginWithExtension() {
      const allInjected = await web3Enable("lip_token");
      console.log(allInjected);
      const allAccounts = await web3Accounts();
      console.log(allAccounts);

      const injector = await web3FromAddress(SENDER);
      return injector;
    },
    async setupLipTokenContract() {
      const contractAddress =
        "5ECRw65o9wU72MN4MPn3A8aBP5FAgy9PDv9qAjMBH9F8uy8V";

      if (!this.api) return;
      this.lipTokenContract = new ContractPromise(
        this.api,
        metadata,
        contractAddress
      );
    },
  },
});
</script>
<style>
</style>