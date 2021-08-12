<template>
  <v-flex>
    <v-layout row wrap>
      <v-flex xs12 class="pb-3">
        <span class="vb-cp-title">{{$t('message.vBusinessWebSite')}}</span>
        <span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessWebSiteDetail')}}</span>
      </v-flex>
    </v-layout>    
    <v-layout row wrap style="heigth: 100%!important;">
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-text-field
          ref="navigationID"
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          hide-details
          clearable
          required
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.vBusinessStepFinalNavigationID')"
        ></v-text-field>
      </v-flex>
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-text-field
          ref="navigationPreview"
          hide-details
          required
          :readonly="true" 
          :color="currentBussinesClub.theme.primary"
          :label="$t('message.vBusinessStepFinalUrlPreview')"
          :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
        ></v-text-field>
      </v-flex>
    </v-layout>
    <v-layout row wrap>
      <v-flex xs12 class="pb-3">
        <span class="vb-cp-title">{{$t('message.vBusinessWebSiteImage')}}</span>
        <span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessWebSiteImageDetail')}}</span>
      </v-flex>
    </v-layout>  
  <!--   <v-layout>
    <v-flex xs12 sm12 md6 lg6 xl12>
          <v-layout wrap row>
            <v-flex
              style="height: height: 130px;"
              :class="$vuetify.breakpoint.smAndUp?'xs4 lg4 sm4 xl4':'xs6 lg6 sm6 xl6'"
            >
              <v-flex
                class="cardServices"
                @click="openSchedulesGestor"
                :class="$vuetify.breakpoint.smAndUp?'marginsTall':'marginsSmallRigth'"
              >
                <v-layout wrap row>
                  <v-flex xs12 lg12 sm12 md12 xl12>
                    <span>Horarios Globales</span>
                  </v-flex>
                  <v-flex style="margin-top: 10px;"  xs12 lg12 sm12 md12 xl12>
                    <v-icon :color="currentBussinesClub.theme.primary">mdi mdi-calendar-clock</v-icon>
                  </v-flex>
                </v-layout>
              </v-flex>
            </v-flex>
            <v-flex
              style="height: height: 130px;"
              :class="$vuetify.breakpoint.smAndUp?'xs4 lg4 sm4 xl4':'xs6 lg6 sm6 xl6'"
            >
              <v-flex
                class="cardServices"
                :class="$vuetify.breakpoint.smAndUp?'marginsTall':'marginSmallLeft'"
              >
                <v-layout wrap row>
                  <v-flex xs12 lg12 sm12 md12 xl12>
                    <span>Horarios Especiales</span>
                  </v-flex>
                  <v-flex style="margin-top: 10px;"  xs12 lg12 sm12 md12 xl12>
                    <v-icon :color="currentBussinesClub.theme.primary">mdi mdi-calendar-star</v-icon>
                  </v-flex>
                </v-layout>
              </v-flex>
            </v-flex>
            <v-flex
              style="height: height: 130px;"
              :class="$vuetify.breakpoint.smAndUp?'xs4 lg4 sm4 xl4':'xs12 pt-3'"
            >
              <v-flex
                :class="$vuetify.breakpoint.smAndUp?'marginsTallLastItem':'xs6 centerDiv'"
                class="cardServices"
              >
                <v-layout wrap row>
                  <v-flex xs12 lg12 sm12 md12 xl12>
                    <span>Servicios</span>
                  </v-flex>
                  <v-flex style="margin-top: 10px;" xs12 lg12 sm12 md12 xl12>
                    <v-icon :color="currentBussinesClub.theme.primary">mdi mdi-hand-okay</v-icon>
                  </v-flex>
                </v-layout>
              </v-flex>
            </v-flex>
          </v-layout>
        </v-flex>
    </v-layout> -->
     
  </v-flex>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import AppConfig from "Constants/AppConfig";
import confirmDialog from "../../../../../components/ySocialHome/confirmDialog";
export default {
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  watch: {},
  created() {},
  mounted() {},
  data() {
    return {};
  },
  methods: {}
};
</script>

