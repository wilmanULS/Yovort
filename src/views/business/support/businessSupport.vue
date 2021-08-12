<template>
  <div>
    <v-layout row wrap align-center justify-center>
      <v-flex xs12 sm12 md6 lg6 xl6>
        <v-tabs flat small :slider-color="currentBussinesClub.theme.primary" grow>
          <v-tab @click="activeView = 0">{{$t('message.tickets')}}</v-tab>
          <v-tab @click="activeView = 1">{{$t('message.frequentQuestions')}}</v-tab>
        </v-tabs>
      </v-flex>
    </v-layout>
    <div style="padding-top:10px;" v-if="activeView == 0">
      <v-tickets></v-tickets>
    </div>
    <div style="padding-top:10x;" v-if="activeView == 1">
      <v-faqs></v-faqs>
    </div>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
import tickets from "./tickets/tickets";
import faqs from "./faqs/faqs";
export default {
  components: {
    "v-tickets": tickets,
    "v-faqs": faqs
  },
  computed: {
    ...mapGetters(["currentBussinesClub"])
  },
  data() {
    return {
      categoryList: [],
      selected: [],
      addNewFaq: false,
      activeView: 0,
      chips: [],
      items: ["Streaming", "Eating"],
      desserts: [{}]
    };
  }
};
</script>