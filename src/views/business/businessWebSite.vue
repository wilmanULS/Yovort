<template>
  <v-flex>
    <v-layout row wrap>
      <v-flex xs12>
        <span class="vb-cp-title">{{$t('message.vBusinessWebSite')}}</span>
        <span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessWebSiteDetail')}}</span>
      </v-flex>
    </v-layout>
  </v-flex>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import AppConfig from "Constants/AppConfig";
import confirmDialog from "../../components/ySocialHome/confirmDialog";
export default {
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
  data() {
    return {      
    };
  },
  methods: {
  }
};
</script>

