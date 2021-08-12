<template>
  <v-hover>
    <v-card
      class="borderCards flexcard"
      :style="'height: 300px!important;'"
      slot-scope="{ hover }"
      :class="`elevation-${hover ? 12 : 2}`"
    >
  
     <v-container >
        <v-layout row   wrap  >
          <v-flex xs4 style="height:100%!important;"> 
            <v-avatar :size="$vuetify.breakpoint.xs?90:
                             $vuetify.breakpoint.sm?130:
                             $vuetify.breakpoint.md?80:
                             $vuetify.breakpoint.lg?75:
                             110">
					  <v-img                  
                  :src="`${FMS_URL}/image/fetch/${getObjectPath(businessInfo[0], 'businessImage.logoLarge','business/a681f1fa-0bf4-4f24-95a8-50941299d856.png')}`"            
            >  </v-img>   
					</v-avatar>          
                  
          </v-flex>
   
          <v-flex xs8 style="margin-top:2%">
            <v-flex class="ellipsis fs-12pt headline mb-0" style="color:black; font-weight:bold;">
              <v-tooltip bottom>
                <template v-slot:activator="{ on }">
                  <span
                    v-on="on"
                  >{{getObjectPath(businessInfo[0], 'identity.name',$t('message.businessNoName'))}}</span>
                  <br>
                </template>
                <span>{{getObjectPath(businessInfo[0], 'identity.name',$t('message.businessNoName'))}}</span>
              </v-tooltip>
            </v-flex>
            <v-flex class="ellipsis" style="color:black;">
              <flag
                :iso="getItemName(getObjectPath(businessInfo[0], 'identity.operationCountry',''),'flag')"
              />
              &nbsp
              {{getObjectPath(businessInfo[0], 'addresses.0.address.city','')}}
            </v-flex>        
          </v-flex> 
        </v-layout>
               <v-layout row  wrap  >
 
          <v-flex xs12>
           
   
            <v-flex class="ellipsis" style="color:black;">
              <v-tooltip bottom>
                <template v-slot:activator="{ on }">
                  <v-icon v-on="on" :color="currentBussinesClub.theme.primary">mdi-briefcase-account</v-icon>
                  
                  <v-tooltip bottom>
                    <template v-slot:activator="{ on }">
                      <span
                        v-on="on"
                      > {{getObjectPath(businessInfo[0], 'identity.identification',$t('message.businessNoId'))}}</span>
                    </template>
                    <span>{{getObjectPath(businessInfo[0], 'identity.identification',$t('message.businessNoId'))}}</span>
                  </v-tooltip>
                </template>
                <span>{{$t('message.identification')}}</span>
              </v-tooltip>
            </v-flex>
            <v-flex class="ellipsis" style="color:black;">
              <v-tooltip bottom>
                <template v-slot:activator="{ on }">
                  <v-icon v-on="on" :color="currentBussinesClub.theme.primary">mdi-briefcase-search</v-icon>
                </template>
                <span>{{$t('message.state')}}</span>
              </v-tooltip>

              <span style="color:black" v-if="getObjectPath(businessInfo[0], 'status',-1)==1"> En edición</span>
              <span style="color:black" v-if="getObjectPath(businessInfo[0], 'status',-1)==2"> Creado</span>
            </v-flex>
            <v-flex class="ellipsis" style="color:black;">
              <v-tooltip bottom>
                <template v-slot:activator="{ on }">
                  <v-icon v-on="on" :color="currentBussinesClub.theme.primary">mdi-calendar-clock</v-icon>                    
                </template>
                <span>{{$t('message.lastModified')}}</span>
              </v-tooltip>
                <span style="color:black">
                      {{getLastUpdated(businessInfo[0].updatedAt)}}
                  </span>
            
            </v-flex>
            
           
          </v-flex>
                  
 
        </v-layout>
      </v-container>  
      <v-card-actions>

        <div style="position: absolute!important;right: 10px!important; bottom:5px!important;">
          
          <v-speed-dial
            v-model="fab"
            direction="left"
            open-on-hover
            transition="slide-x-reverse-transition"
          >
            <template v-slot:activator>
              <v-btn v-model="fab" :color="currentBussinesClub.theme.primary" dark fab small>
                <v-icon>mdi mdi-plus</v-icon>
                <v-icon>mdi mdi-close</v-icon>
              </v-btn>
            </template>

            <v-btn
              v-if="getObjectPath(businessInfo[0], 'status',-1)==1"
              @click="ContinueSetUp(businessInfo[0].id)"
              :color="currentBussinesClub.theme.secondary"
              :title="$t('message.vBusinessDoContinueWizard')"
              fab
              dark
              small
              style="margin-right: 10px!important;"
            >
              <v-icon>mdi mdi-assistant</v-icon>
            </v-btn>
            <v-btn
              v-if="getObjectPath(businessInfo[0], 'status',-1)==2"
              :color="currentBussinesClub.theme.secondary"
              :title="$t('message.vBusinessDoManage')"
              fab
              dark
              small
              style="margin-right: 10px!important;"
              @click="doManage(businessInfo[0])"
            >
              <v-icon>mdi mdi-settings</v-icon>
            </v-btn>
            <v-btn
              v-if="getObjectPath(businessInfo[0], 'status',-1)==1"
              :color="currentBussinesClub.theme.primary"
              :title="$t('message.vBusinessDelete')"
              fab
              dark
              small
              @click="deleteBusiness(businessInfo[0].id,getObjectPath(businessInfo[0], 'identity.name',$t('message.businessNoName')))"
              style="margin-right: 10px!important;"
            >
              <v-icon>mdi mdi-trash-can</v-icon>
            </v-btn>
          </v-speed-dial>
        </div>
      </v-card-actions>
    </v-card>
  </v-hover>
</template>

<script>
import Vue from "vue";
import router from "../../router";
import { mapGetters } from "vuex";
import { EventBus } from "../../eventBus.js";
import appConfig from "Constants/AppConfig";
import { log } from "util";

export default {
  props: {
    businessInfo: [Array]
  },
  components: {},
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
  mounted() {},
  created() {
    this.$store.dispatch("doGetCountries");
  },
  methods: {
    getLastUpdated(date){
      let updatedAt="";
      let transformDate = new Date(date)
      let currentDate = new Date()
      let calendarDate = transformDate.getFullYear() + "-" + this.putZeros(transformDate.getMonth() + 1) + "-" + this.putZeros(transformDate.getDate());
      let now = currentDate.getFullYear() + "-" + this.putZeros(currentDate.getMonth() + 1) + "-" + this.putZeros(currentDate.getDate());
      let timeDate = this.putZeros(transformDate.getHours()) + ":" + this.putZeros(transformDate.getMinutes()); 
      updatedAt=calendarDate;
      if(Date(calendarDate)==Date(now)){
        updatedAt=this.$t("message.todayAt") + timeDate;
      }
      return updatedAt      
    },
    putZeros(n){
      if(n <= 9){
        return "0" + n;
      }
      return n
    },
    doManage(data) {
      let info = {
        id: data.id,
        name: data.identity.name,
        identification: data.identity.identification,
        updatedAt: data.updatedAt
      };
      this.$emit("onDoManage", info);
    },

    hexToRGB(hex, alpha) {
      hex = hex.replace("#", "");
      let r = parseInt(
        hex.length == 3 ? hex.slice(0, 1).repeat(2) : hex.slice(0, 2),
        16
      );
      let g = parseInt(
        hex.length == 3 ? hex.slice(1, 2).repeat(2) : hex.slice(2, 4),
        16
      );
      let b = parseInt(
        hex.length == 3 ? hex.slice(2, 3).repeat(2) : hex.slice(4, 6),
        16
      );
      return r + ", " + g + ", " + b + ", " + alpha;
    },
    ContinueSetUp(id) {
      this.$emit("onWizard", id);
    },
    deleteBusiness(id, name) {
      let data = {
        id: id,
        name: name
      };
      this.$emit("onDeleteBusiness", data);
    },
    getItemName(item, value) {
      let val = this.countries.filter(country => country.countryId == item);
      return val.length > 0 ? val[0][value] : "";
    }
  },
  data() {
    return {
      FMS_URL: appConfig.FMS_URL,
      getObjectPath: Vue.getObjectPath,
      viewReady: true,
      fab: false      
    };
  }
};
</script>
  <style scoped>
</style>