<template>
  <v-flex v-if="viewIsReady">
    <v-layout row wrap>
      <v-card
        style="heigth: 100%!important; width:100%!important;border-radius: 2px;background:#F4F7FA"
        :class="{'mr05': $vuetify.breakpoint.mdAndUp}"
        flat
      >
        <v-card-title>
          <v-layout row wrap>
            <v-flex
              :xs3="$vuetify.breakpoint.lgAndDown"
              :xs2="$vuetify.breakpoint.xl"
              class="profile-flex-padding"
              v-if="$vuetify.breakpoint.mdAndUp"
            >
              <v-card>
                <v-card-actions
                  class="dialog-title linear-gradient"
                  card
                  dark
                  style="cursor:pointer"
                  @click="$emit('onClose')"
                >
                  <v-btn flat icon dark style="margin-right: 2%!important;margin-left:4%!important">
                    <v-icon>mdi-home-circle</v-icon>
                  </v-btn>

                  <v-card-title
                    style="padding-left: 0!important;font-size:18px"
                  >{{$t('message.vBusinessCpMenuRedirect')}}</v-card-title>
                </v-card-actions>
                <v-list>
                  <v-container
                    style="padding-left:0px!important;padding-bottom:0px!important;padding-top:0px!important"
                  >
                    <v-layout row fill-height align-center justify-center wrap>
                      <v-flex xs4 style="height:100%!important;">
                        <v-img
                          :src="`${FMS_URL}/image/fetch/${getObjectPath(infoBusiness, 'logoLarge',
									'business/a681f1fa-0bf4-4f24-95a8-50941299d856.png')}`"
                          class="rounded-circle img-responsive"
                          style="height:100%!important"
                        ></v-img>
                      </v-flex>
                      <v-flex xs8 style="margin-top:2%; padding-left:5px!important">
                        <v-flex
                          class="ellipsis fs-12pt mb-0"
                          style="color:black; font-weight:bold;"
                        >
                          <v-tooltip bottom>
                            <template v-slot:activator="{ on }">
                              <span v-on="on">{{infoBusiness.name}}</span>
                              <br>
                            </template>
                            <span>{{infoBusiness.name}}</span>
                          </v-tooltip>
                        </v-flex>
                        <v-flex
                          class="ellipsis"
                          style="color:black;"
                          v-if="infoBusiness.identification"
                        >
                          <v-tooltip bottom>
                            <template v-slot:activator="{ on }">
                              <v-tooltip bottom>
                                <template v-slot:activator="{ on }">
                                  <span v-on="on">{{infoBusiness.identification}}</span>
                                </template>
                                <span>{{infoBusiness.identification}}</span>
                              </v-tooltip>
                            </template>
                            <span>{{$t('message.identification')}}</span>
                          </v-tooltip>
                        </v-flex>
                        <v-flex style="color:black;">
                          <h5
                            style="color:black"
                          >{{$t('message.lastModified')}}: {{getLastUpdated(infoBusiness.updatedAt)}}</h5>
                        </v-flex>
                      </v-flex>
                    </v-layout>
                  </v-container>
                  <v-divider class="styleDivider"></v-divider>
                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(0)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi mdi-information</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessInfo')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>

                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(1)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi mdi-notebook</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessAbout')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>

                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(2)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi mdi-contact-mail</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessContact')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>
                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(3)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi mdi-account-group</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessUsers')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>
                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(4)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi mdi-account-multiple-check</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessClients')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>
                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(5)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi mdi-cart-arrow-down</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessProducts')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>
                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(6)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi mdi-web</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessWebPage')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>
                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(7)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi mdi-briefcase</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessBriefcase')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>
                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(8)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi-store</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessVStore')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>
                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(10)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi mdi-bullhorn</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessCampaing')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>
                  <v-list-tile class="sidebarItem" avatar @click="SwitchView(11)">
                    <v-list-tile-avatar>
                      <v-icon class="sidebar-icon">mdi mdi-headset</v-icon>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title
                        class="sidebar-title"
                      >{{$t('message.vBusinessCpMenuBusinessSupport')}}</v-list-tile-title>
                    </v-list-tile-content>
                  </v-list-tile>
                </v-list>
              </v-card>
            </v-flex>
            <v-flex
              :xs9="$vuetify.breakpoint.lgAndDown"
              :xs10="$vuetify.breakpoint.xl"
              :xs12="$vuetify.breakpoint.smAndDown"
              class="profile-flex-padding"
            >
              <v-card>
                <v-card-actions
                  class="dialog-title linear-gradient"
                  card
                  dark
                  v-if="$vuetify.breakpoint.smAndDown"
                >
                  <v-btn @click="$emit('onClose')" flat icon dark style=" margin-left:4%!important">
                    <v-icon>mdi-home-circle</v-icon>
                  </v-btn>

                  <v-spacer></v-spacer>
                  <div style="padding-right:10px;">
                    <v-menu transition="slide-y-transition" left attach>
                      <template v-slot:activator="{ on }">
                        <v-btn flat dark v-on="on">
                          {{$t('message.vBusinessCpMenuOptions')}}
                          <v-icon right>mdi mdi-menu</v-icon>
                        </v-btn>
                      </template>
                      <v-card>
                        <v-list>
                          <v-list-tile avatar>
                            <v-list-tile-avatar :size="$vuetify.breakpoint.xs?45:50">
                              <img
                                :src="`${FMS_URL}/image/fetch/${getObjectPath(infoBusiness, 'logoLarge',
									'business/a681f1fa-0bf4-4f24-95a8-50941299d856.png')}`"
                              >
                            </v-list-tile-avatar>
                            <v-list-tile-content>
                              <v-list-tile-title>
                                <v-flex
                                  class="ellipsis fs-12pt mb-0"
                                  style="color:black; font-weight:bold;"
                                >
                                  <v-tooltip bottom>
                                    <template v-slot:activator="{ on }">
                                      <span v-on="on">{{infoBusiness.name}}</span>
                                      <br>
                                    </template>
                                    <span>{{infoBusiness.name}}</span>
                                  </v-tooltip>
                                </v-flex>
                              </v-list-tile-title>
                              <v-list-tile-sub-title>
                                <v-flex style="color:black;">
                                  <h5
                                    style="color:black"
                                  >{{$t('message.lastModified')}}: {{getLastUpdated(infoBusiness.updatedAt)}}</h5>
                                </v-flex>
                              </v-list-tile-sub-title>
                            </v-list-tile-content>
                          </v-list-tile>
                        </v-list>
                        <v-divider></v-divider>
                        <v-list>
                          <v-list>
                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(0)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi mdi-information</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessInfo')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>

                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(1)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi mdi-notebook</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessAbout')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>

                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(2)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi mdi-contact-mail</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessContact')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>
                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(3)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi mdi-account-group</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessUsers')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>
                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(4)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi mdi-account-multiple-check</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessClients')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>
                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(5)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi mdi-cart-arrow-down</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessProducts')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>
                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(6)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi mdi-web</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessWebPage')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>
                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(7)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi mdi-briefcase</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessBriefcase')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>
                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(8)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi-store</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessVStore')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>
                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(9)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi mdi-bullhorn</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessCampaing')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>
                            <v-list-tile class="sidebarItem" avatar @click="SwitchView(10)">
                              <v-list-tile-avatar>
                                <v-icon class="sidebar-icon">mdi mdi-headset</v-icon>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title
                                  class="sidebar-title"
                                >{{$t('message.vBusinessCpMenuBusinessSupport')}}</v-list-tile-title>
                              </v-list-tile-content>
                            </v-list-tile>
                          </v-list>
                        </v-list>
                      </v-card>
                    </v-menu>
                  </div>
                </v-card-actions>
                <v-flex
                  :class="{'vb-padding-cp': $vuetify.breakpoint.mdAndUp, 'vb-padding-cp-sm':$vuetify.breakpoint.smAndDown }"
                >
                  <div v-if="activeView == 0">
                    <v-business-data></v-business-data>
                  </div>
                  <div v-if="activeView == 1">
                    <v-business-information></v-business-information>
                  </div>
                  <div v-if="activeView == 2">
                    <v-business-contact></v-business-contact>
                    <!--On construction -->
                    <!-- 		<v-container grid-list-xs px-5>
										<center>
											<v-img width="500px" src="/static/img/under_construction.gif"></v-img>
										</center>
                    </v-container>-->
                  </div>
                  <div v-if="activeView == 3">
                    <!-- 	<v-business-users></v-business-users> -->
                  <!--  On construction -->
                    <v-container grid-list-xs px-5>
                      <center>
                        <v-img width="500px" src="/static/img/under_construction.gif"></v-img>
                      </center>
                    </v-container> 
                  </div>
                  <div v-if="activeView == 4">
                    <!--On construction -->
                    <v-container grid-list-xs px-5>
                      <center>
                        <v-img width="500px" src="/static/img/under_construction.gif"></v-img>
                      </center>
                    </v-container>
                  </div>
                  <div v-if="activeView == 5">
                    <v-products-list></v-products-list>
                  </div>
                  <div v-if="activeView == 6">
                     <v-business-website></v-business-website> 
                    <!-- <v-container grid-list-xs px-5>
                      <center>
                        <v-img width="500px" src="/static/img/under_construction.gif"></v-img>
                      </center>
                    </v-container> -->
                  </div>
                  <div v-if="activeView == 7">
                    <!--On construction -->
                  </div>
                  <div v-if="activeView == 8">
                    <v-business-store-panel v-bind:storeData="storeData"></v-business-store-panel>
                  </div>
                  <div v-if="activeView == 9"></div>

                  <div v-if="activeView == 10">
                    <v-container grid-list-xs px-5>
                      <center>
                        <v-img width="500px" src="/static/img/under_construction.gif"></v-img>
                      </center>
                    </v-container>
                  </div>
                  <div v-if="activeView == 11">
                    <v-business-support></v-business-support>
                  </div>
                  <div></div>
                </v-flex>
              </v-card>
            </v-flex>
          </v-layout>
        </v-card-title>
      </v-card>
    </v-layout>
  </v-flex>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import router from "../../router";
import { EventBus } from "../../eventBus.js";
import BusinessData from "./businessData";
import BusinessSupport from "./support/businessSupport";
import BusinessContact from "./businessContact";
import BusinessInformation from "./businessInformation";
import BusinessUsers from "./businessUsers";
import BusinessWebSite from "./control/webSite/businessWebSite";
import Products from "./product/productList";
import StorePanel from "./store/storePanelTab";
import appConfig from "Constants/AppConfig";
import { constants } from "crypto";
export default {
  components: {
    "v-business-data": BusinessData,
    "v-business-support": BusinessSupport,
    "v-business-contact": BusinessContact,
    "v-business-information": BusinessInformation,
    "v-business-users": BusinessUsers,
    "v-business-website": BusinessWebSite,
    "v-products-list": Products,
    "v-business-store-panel": StorePanel
  },
  props: {
    infoBusiness: Object
  },
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "userBusinesses",
      "businessSelected",
      "businessControlPanel",
      "businessId",
      "getStores"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  created() {
    /* this.$root.$children[0].$emit(
		  "showLoading",
		  "Cargando información del negocio"
		);
		this.$store
		  .dispatch("doGetBusinessControlPanel", {
		    businessId: this.businessId
		  })
		  .then(() => { */
    this.viewIsReady = true;
    /*   this.$root.$children[0].$emit("hideLoading");
		}); */
  },

  data() {
    return {
      FMS_URL: appConfig.FMS_URL,
      getObjectPath: Vue.getObjectPath,
      viewIsReady: false,
      activeView: 0,
      storeData: {}
    };
  },
  mounted() {
    var options = {
      shouldSort: true,
      threshold: 0,
      location: 0,
      distance: 100,
      maxPatternLength: 32,
      minMatchCharLength: 1,
      keys: ["author.firstName"]
    };
    var list = [];
    for (const key in this.getStores) {
      if (this.getStores.hasOwnProperty(key)) {
        const business = this.getStores[key][0];
        if (business.businessId == this.businessId) {
          if (business.stores.length != 0) {
            this.$store.dispatch("setBusinessStore", business);
          }
        }
      }
    }
  },
  methods: {
    getLastUpdated(date) {
      let updatedAt = "";
      let transformDate = new Date(date);
      let currentDate = new Date();
      let calendarDate =
        transformDate.getFullYear() +
        "-" +
        this.putZeros(transformDate.getMonth() + 1) +
        "-" +
        this.putZeros(transformDate.getDate());
      let now =
        currentDate.getFullYear() +
        "-" +
        this.putZeros(currentDate.getMonth() + 1) +
        "-" +
        this.putZeros(currentDate.getDate());
      let timeDate =
        this.putZeros(transformDate.getHours()) +
        ":" +
        this.putZeros(transformDate.getMinutes());
      updatedAt = calendarDate;
      if (Date(calendarDate) == Date(now)) {
        updatedAt = this.$t("message.todayAt") + timeDate;
      }
      return updatedAt;
    },
    putZeros(n) {
      if (n <= 9) {
        return "0" + n;
      }
      return n;
    },
    SwitchView(view) {
      this.activeView = view;
    }
  }
};
</script>
  <style scoped>
.shadow {
  width: 100px;
  margin: 0 auto;
  height: 100px;
  background-color: #f5f5f5;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 40px;
}
.bottom {
  box-shadow: 0px 15px 10px -15px #111;
}
.styleDivider {
  margin-left: 5px;
  margin-right: 5px;
  padding-bottom: 2px;
}
</style>
