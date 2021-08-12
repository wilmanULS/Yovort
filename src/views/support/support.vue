<template>
  <div style="margin-top: -25px!important;">
    <v-container class="profile-empty-container no-padding">
      <v-layout>
        <v-flex xs12 lg12>
          <v-card class="desktop-image-profile" flat>
            <v-card-text class="no-padding">
              <v-tabs
                slot="extension"
                v-model="tabs"
                :slider-color="currentBussinesClub.theme.primary"
                color="rgba(0,0,0,0)"
                grow
                :show-arrows="$vuetify.breakpoint.smAndDown"
              >
                <v-tab>{{$t('message.frequentQuestions')}}</v-tab>
                <v-tab >{{$t('message.tickets')}}</v-tab>
              </v-tabs>
            </v-card-text>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
    <v-container class="no-padding padding-top-24">
      <v-tabs-items touchless v-model="tabs">
        <v-tab-item :transition="false" :reverse-transition="false">
          <v-faqs></v-faqs>
        </v-tab-item>
        <v-tab-item :transition="false" :reverse-transition="false">
            <div>
              <v-tickets> </v-tickets>
            </div>
        </v-tab-item>
      </v-tabs-items>
    </v-container>
  </div>
</template>
<script>
import { mapGetters } from "vuex";
import faqs from "./faqs"
import tickets from "./tickets"
export default {
  computed: {
    ...mapGetters(["userProfile", "currentBussinesClub"])
  },
  components:{
    "v-faqs": faqs,
    "v-tickets":tickets
  },
  data(){
      return{
          tabs: 0,
      }
  }
};
</script>
