 <!--<template>
  <div v-if="viewIsReady">
    <v-layout row wrap mb-3>
      <v-flex xs12 md12>
        <h4 class="grey--text">
          <strong>{{$t('message.vProductSelectType')}}</strong>
        </h4>
      </v-flex>
  </v-layout>
    <v-layout 
      v-if="$vuetify.breakpoint.mdAndUp"
      style="width:100%!important"
      row
      wrap
      fill-height
      justify-center
      align-center
    >
      <v-flex style="width:20%!important" v-for="(type,index) in businessType" :key="index">
        <v-card
          ma-0
          pa-0
          style="border:solid 2px #f0f0f0; width:90%; border-radius: 6px;"
          flat
          :ref="`card_${type.code}`"
          @click="hoverCard(type)"
          class="card"
          :class="{'selected': isSelected(index)}"
        >
          <v-img :src="`${FMS_URL}/image/fetch/${type.image}`">
            <template v-slot:placeholder>
              <v-layout fill-height align-center justify-center ma-0>
                <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
              </v-layout>
            </template>
          </v-img>
          <v-card-text class="card-footer">Producto</v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
    <v-layout
      style="width:100%!important"
      row
      wrap
      fill-height
      justify-center
      align-center
      v-if="$vuetify.breakpoint.smAndDown"
    >
      <v-flex>
        <v-container>
          <v-card flat color="transparent">
            <carousel-3d
              ref="carousel"
              :startIndex="0"
              :perspective="0"
              :space="220"
              :display="5"
              :width="200"
              :height="120"
              @after-slide-change="onAfterSlideChange"
            >
              <slide
                v-bind:id="index+'image'"
                :index="index"
                v-for="(type,index) in businessType"
                :key="index"
                v-bind:style="index==selectedCard?'border: 4px solid var(--primary)!important;':''"
              >
                <v-img :src="`${FMS_URL}/image/fetch/${type.image}`">
                  <template v-slot:placeholder>
                    <v-layout fill-height align-center justify-center ma-0>
                      <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
                    </v-layout>
                  </template>
                </v-img>
              </slide>
            </carousel-3d>
          </v-card>
        </v-container>
      </v-flex>
    </v-layout>
    <transition name="fade">
      <div v-if="selectedCard>=0">
        <v-layout row wrap mt-3>
          <v-flex xs12 md12>
            <h4 class="grey--text">
              <strong>{{businessTypeNameSelected}}</strong>
            </h4>
          </v-flex>
        </v-layout>
        <v-layout row wrap mb-3>
          <v-flex xs12 md12>
            <h5 class="grey--text" style=" text-align: justify;">{{businessTypeDescriptionSelected}}</h5>
          </v-flex>
        </v-layout>
        <v-layout row wrap>
          <v-btn
            :color="currentBussinesClub.theme.primary"
            dark
            style="margin-left:0px"
            @click="SaveBusinessType()"
          >{{$t('message.vBusinessStepBusinessContinue')}}</v-btn>
        </v-layout>
      </div>
    </transition>
   
  </div>
</template> */
<script>
import Vue from "vue";
import appConfig from "Constants/AppConfig";
import { EventBus } from "../../../../eventBus.js";
import { mapGetters } from "vuex";

export default {
  components: {

  },
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "businessType",    
      "businessSelected",
      "businessId"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    },
    currentUserBusiness: {
      get() {
        if (this.$store.getters.currentUserBusiness != "")
          return this.$store.getters.currentUserBusiness;
      }
    }
  },
  mounted() {
    if (this.$vuetify.breakpoint.xs) {
      if (this.businessSelected.businessModel) {
        this.onAfterSlideChange(
          this.businessSelected.businessModel[0]
            ? this.businessType.findIndex(
                x =>
                  x.code == this.businessSelected.businessModel[0].businessType
              )
            : 0
        ); 
      } else {
        this.onAfterSlideChange(0);
      }
    }
    this.$root.$children[0].$emit("hideLoading");
  },

  created() {
    EventBus.$on("clearWizard", () => {
      this.ClearData();
    });
    
    this.$store
      .dispatch("doGetBusinessType")
      .then(() => {
      
        if (this.businessSelected.businessModel) {
          this.hoverCard(
            this.businessType[
              this.businessType.findIndex(
                x =>
                  x.code == this.businessSelected.businessModel[0].businessType
              )
            ]
          );
        }
      })
      .finally(() => {
        this.viewIsReady = true;
      });
  },
  methods: {
    onAfterSlideChange(index) {
      this.businessTypeSelected = this.businessType[index].code;
      this.selectedCard = index;
      this.businessTypeNameSelected = this.businessType[index].name;
      this.businessTypeDescriptionSelected = this.businessType[
        index
      ].description;
    },
    ClearData() {
      this.selectedCard = -1;
      this.businessTypeNameSelected = "";
      this.businessTypeDescriptionSelected = "";
    },
    viewTermsConditions() {
      this.$refs.termsDialog.openDialog();
    },
    SaveBusinessType() {
     
        this.$store.dispatch("doCreateUserProducts", { businessId: this.businessId })
          .then(res => {
            this.$emit("onContinue");
            this.$store.dispatch("doGetUserProducts",this.businessId);
          }).catch(err => {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.couldNotAddUserBusiness"),
              type: "error"
            });
          });
    }, 
    hoverCard(item) {
      this.businessTypeSelected = item.code;
      this.selectedCard = this.businessType.findIndex(x => x.code == item.code);
      this.businessTypeNameSelected = item.name;
      this.businessTypeDescriptionSelected = item.description;
    },
    isSelected(index) {
      return this.selectedCard === index;
    }
  },
  data() {
    return {
      
      viewIsReady: false,
      FMS_URL: appConfig.FMS_URL,
      businessTypeNameSelected: "",
      businessTypeDescriptionSelected: "",
      selectedCard: -1,
      businessTypeSelected: null
    };
  }
};
</script>
<style>

</style>
-->
