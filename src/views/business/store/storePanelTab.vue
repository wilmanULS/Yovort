<template>
  <v-layout row wrap class="tabsList">
    <v-tabs color="white" v-model="tabs" :show-arrows="$vuetify.breakpoint.smAndDown">
      <v-tabs-slider :color="currentBussinesClub.theme.primary"></v-tabs-slider>
      <v-tab>{{$t("message.vStoreMenuDetailStore")}}</v-tab>
      <v-tab>{{$t("message.vStoreMenuCustomization")}}</v-tab>
      <v-tab>{{$t("message.vStoreMenuBanner")}}</v-tab>
      <v-tabs-items v-model="tabs" height="100%">
        <v-tab-item :transition="false" :reverse-transition="false">
          <v-store-panel-detail ></v-store-panel-detail>
        </v-tab-item>
        <v-tab-item :transition="false" :reverse-transition="false">
          <v-store-panel-customization ></v-store-panel-customization>
        </v-tab-item>
        <v-tab-item :transition="false" :reverse-transition="false">
          <v-store-panel-banner ></v-store-panel-banner>
        </v-tab-item>
      </v-tabs-items>
    </v-tabs>
  </v-layout>
</template>

<script>
import { mapGetters } from "vuex";
import StoreDetail from "./storeDetail";
import StoreCustom from "./storeCustom";
import StoreBanner from "./storeBanner";
export default {
  data() {
    return {
      tabs: 0
    };
  },
  components: {
    "v-store-panel-detail": StoreDetail,
    "v-store-panel-customization": StoreCustom,
    "v-store-panel-banner": StoreBanner
  },
  computed: {
    ...mapGetters([
      "currentBussinesClub",
      "businessSelected",
      "businessId",
      "getStoreById"
    ])
  },
  created() {},
  mounted() {
  },
  methods: {}
};
</script>

<style scoped>
.tabsList .v-tabs {
  width: 100%;
}
</style>
