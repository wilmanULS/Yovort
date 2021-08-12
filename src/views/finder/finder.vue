<template>
  <v-flex>
    <v-flex>
      <v-map></v-map>
      <v-tab  v-show="!showDetailLocation"></v-tab>
    </v-flex>
    <v-flex>
      <v-flex  @click="openSettingBar()" v-if="showSettingsButton" class="settingsButton">
        <span>
        <i  class="fas fa-sliders-h centerElement"  style="color:white; cursor: pointer"></i>
      </span>
      </v-flex>
      <v-settings-side-bar></v-settings-side-bar>
      <v-detail-location v-if="showDetailLocation" v-bind:location="locationData"></v-detail-location>
    </v-flex>
  </v-flex>
</template>
<script>
import Vue from "vue";

import { mapGetters } from "vuex";

import Map from "./map";
import Tabs from "./tabs";
import SettingsSideBar from './settingsSideBar'
import Details from "./details";
import {
    EventBus
} from "../../eventBus";

export default {
  computed: {
    ...mapGetters(["currentBussinesClub"]),
    currentUserBusiness: {
      get() {
        if (this.$store.getters.currentUserBusiness != "")
          return this.$store.getters.currentUserBusiness;
      }
    }
  },
  beforeDestroy(){
    EventBus.$off("openSettingsSideBar")
  },
  components: {
    "v-map": Map,
    "v-tab": Tabs,
    "v-settings-side-bar":SettingsSideBar,
    "v-detail-location":Details
  },
  mounted(){
    let el=this;
    this.$el.style.setProperty(
      "--colorButtom",
      this.currentBussinesClub.theme.secondary
    );
    EventBus.$on("showSettingsButton",()=>{
      this.showSettingsButton=true;  
    })
    EventBus.$on("showDetailsLocation",(data)=>{
      this.locationData=data;
      el.showDetailLocation=true;
    })
    EventBus.$on("hideDetailsLocation",()=>{
      el.showDetailLocation=false;
    })
  },
  data(){
    return{
      showSettingsButton:true,
      locationData:{},
      showDetailLocation:false
    }
  },
  methods:{
    openSettingBar(){
        EventBus.$emit("openSettingsSideBar");
        this.showSettingsButton=false;
    }
  }
};
</script>
<style less>
:root {
  --colorButtom: #ffffff;
}

.settingsButton {
  top: 0px;
  right: 0px;
  width: 40px !important;
  height: 40px !important;
  position: absolute;
  background-color: var(--colorButtom) !important;
  z-index: 1;
  font-size: 20px;
}
.centerElement {
  position: relative;
  float: left;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
</style>
