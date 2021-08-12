<template>
  <v-flex>  
    <v-layout row wrap>
      <v-flex xs12 sm12 md6 lg6 xl6>
        <v-card>
          <v-tabs flat   :slider-color="currentBussinesClub.theme.primary" grow>
            <v-tab @click="activeView = 0">
              <v-icon left>mdi-web</v-icon>
          {{$t('message.vBusinessWebSiteHeaderWebSite')}}
            </v-tab>
            <v-tab @click="activeView = 1">
              <v-icon left>mdi-comment-processing-outline</v-icon>
              {{$t('message.vBusinessWebSiteHeaderOpinions')}}
            </v-tab>
            <v-tab @click="activeView = 2">
              <v-icon left>mdi-hand-okay</v-icon>
             {{$t('message.vBusinessWebSiteHeaderServices')}}
            </v-tab>
              <v-tab @click="activeView = 3">
              <v-icon left>mdi-contact-mail</v-icon>
              {{$t('message.vBusinessWebSiteHeaderContacts')}}
            </v-tab>
          </v-tabs>
        </v-card>
      </v-flex>
    </v-layout>
     <div style="padding-top:10px;" v-if="activeView == 0">
      <v-web-site></v-web-site>
    </div>
    <div style="padding-top:10x;" v-if="activeView == 1">
      <v-web-opinions></v-web-opinions>
    </div>
    <div style="padding-top:10x;" v-if="activeView == 2">
      <v-web-services></v-web-services>
    </div>
    <div style="padding-top:10x;" v-if="activeView == 3">
      <v-web-contacts></v-web-contacts>
    </div>
  </v-flex>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import AppConfig from "Constants/AppConfig";
import confirmDialog from "../../../../components/ySocialHome/confirmDialog";
import WebSite from "./web/webSite";
import WebOpninions from "./opinions/opinions";
import WebServices from "./services/services";
import WebContacts from "./contacts/contacts";

export default {
    components: {
    "v-web-site": WebSite,
    "v-web-opinions": WebOpninions,
    "v-web-services": WebServices,
    "v-web-contacts": WebContacts
  },
  data() {
    return {    
            activeView:0  
    };
  },
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  watch: {},
  created() {
    
    
  },
  mounted() {
    
  },
  methods: {
  }
};
</script>

