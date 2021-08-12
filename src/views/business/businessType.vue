<template>
  <div v-if="viewReady">
    <v-layout row wrap>
      <v-flex xs12 md12>
        <h4 class="grey--text">
          <strong>{{$t('message.vBusinessStepBusinessTypeDescription')}}</strong>
        </h4>
      </v-flex>
    </v-layout>
    <v-layout
      v-if="$vuetify.breakpoint.smAndDown"
      style="width:100%!important"
      row
      wrap
      fill-height
      justify-center
      align-center
    >
      <v-flex>
        <v-card color="transparent" flat>
          <carousel-3d
            ref="carousel"
            :startIndex="0"
            :perspective="0"
            :inverse-scaling="1500"
            :space="600"
            :display="5"
            :width="200"
            :height="200"
            @after-slide-change="onAfterSlideChange"
            style="margin-bottom:0px!important;
               height: auto;"
          >
            <slide
              v-bind:id="index+'image'"
              :index="index"
              v-for="(type,index) in businessType"
              :key="index"
              style="border:0px!important;"
            >
              <v-img
                :src="`${FMS_URL}/image/fetch/${type.image}`"
                style="height:200px!important; border-bottom:0px!important"
              ></v-img>
            </slide>
          </carousel-3d>
        </v-card>
        <p class="text-xs-center grey--text">Desliza para ver más opciones</p>
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
          style="border:solid 2px #f0f0f0; width:90%; border-radius: 6px;height: auto;"
          flat
          :ref="`card_${type.code}`"
          @click="hoverCard(type)"
          class="businessType"
          :class="{'selected': isSelected(index)}"
        >
      
       <v-img
            :src="`${FMS_URL}/image/fetch/${type.image}`"
            style="  width: 100%;
            height: auto;"
          >
            <template v-slot:placeholder>
              <v-layout fill-height align-center justify-center ma-0>
                <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
              </v-layout>
            </template>       
          </v-img>
       
         <v-card-text
          :class="{'businessType-footer-md':$vuetify.breakpoint.mdOnly,
                   'businessType-footer':$vuetify.breakpoint.lgOnly,
                   'businessType-footer-xl ':$vuetify.breakpoint.xlOnly}"                  
          >{{type.name}}</v-card-text>  
          
            
        </v-card>
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
            @click="saveBusinessType()"
          >{{$t('message.vBusinessStepBusinessContinue')}}</v-btn>
          <span style="  align-self: center;" class="grey--text">
            {{$t('message.vBusinessStepBusinessDoAccept')}}
            <a
              @click="viewTermsConditions()"
              @click.stop
              style="text-decoration:underline !important"
            >{{$t('message.aceptTermAndConditions')}}</a>
            {{$t('message.vBusinessStepBusinessDoAcceptAnd')}}
            <a
              @click="viewTermsConditions()"
              @click.stop
              style="text-decoration:underline !important"
            >{{$t('message.privacyPolicy')}}</a>
          </span>
        </v-layout>
      </div>
    </transition>
    <v-terms-dialog ref="termsDialog" :heading="$t(' message.termsConditions')"></v-terms-dialog>
  </div>
</template>
<script>
import Vue from "vue";
import appConfig from "Constants/AppConfig";
import TermsDialog from "../session/loginTermsDialog";
import { EventBus } from "../../eventBus.js";
import { mapGetters } from "vuex";

export default {
  components: {
    "v-terms-dialog": TermsDialog
  },
  props: {
    currentBusinessId: String,
    stepData: Object
  },
  data() {
    return {
      viewReady: false,
      FMS_URL: appConfig.FMS_URL,
      businessTypeNameSelected: "",
      businessTypeDescriptionSelected: "",
      selectedCard: -1,
      businessTypeSelected: null
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
      "businessType"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  created() {
    /* Get business type catalog */
    Vue.nextTick(() => {
      this.$store.dispatch("doGetBusinessType").then(() => {
        this.$refs.carousel.setSize();
        this.$refs.carousel.goSlide(0);
        /* If businessType was inserted on db then retrieve it and select card*/
        if (this.stepData.businessType) {
          let index = this.businessType.findIndex(
            x => x.code == this.stepData.businessType
          );
          this.hoverCard(this.businessType[index]);
          this.$refs.carousel.goSlide(index);
        } else {
          this.businessTypeSelected = null;
          this.selectedCard = -1;
          this.businessTypeNameSelected = "";
          this.businessTypeDescriptionSelected = "";
        }
      });
    });
  },
  mounted() {
    if (this.$vuetify.breakpoint.xs || this.$vuetify.breakpoint.sm) {
      if (this.stepData.businessModel) {
        this.onAfterSlideChange(
          this.stepData.businessType
            ? this.businessType.findIndex(
                x => x.code == this.stepData.businessType
              )
            : 0
        );
      } else {
        this.onAfterSlideChange(0);
      }
    }
    this.viewReady = true;
  },
  methods: {
    onAfterSlideChange(index) {
      if (!this.businessType || this.businessType.length == 0) {
        return;
      }
      this.businessTypeSelected = this.businessType[index].code;
      this.selectedCard = index;
      this.businessTypeNameSelected = this.businessType[index].name;
      this.businessTypeDescriptionSelected = this.businessType[
        index
      ].description;
    },
    viewTermsConditions() {
      this.$refs.termsDialog.openDialog();
    },
    saveBusinessType() {
      /* Create a new business if there is not an specified business id*/
      this.$root.$children[0].$emit(
        "showLoading",
        "Guardando información del negocio"
      );
      if (this.currentBusinessId === "") {
        this.$store
          .dispatch("doCreateUserBusiness", {
            businessType: this.businessTypeSelected
          })
          .then(businessCreated => {
            this.$root.$children[0].$emit("hideLoading");
            this.$emit("onContinue");
          })
          .catch(err => {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.couldNotAddUserBusiness"),
              type: "error"
            });
          });
      } else {
        /* Update business type property for current business selected */
        this.$store
          .dispatch("doUpdateUserBusiness", {
            businessId: this.currentBusinessId,
            field: "businessType",
            businessType: this.businessTypeSelected,
            path: "businessType"
          })
          .then(() => {
            this.$root.$children[0].$emit("hideLoading");
            this.$emit("onContinue");
          });
      }
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
  }
};
</script>
<style scope>
.businessType-row {
  display: flex;
  min-width: 780px;
  width: 100%;
  height: 275px;
}

.businessType {
  margin: 10px;
  width: 240px;
  height: 260px;
  transition: height 0.3s, box-shadow 0.3s;
}

.businessType:hover {
  box-shadow: 0 0 20px var(--primary);
}

.businessType-image {
  margin: auto;
   height: 245px;
  transition: height 0.3s, opacity 0.3s;
}

.businessType-image.selected {
  opacity: 0.3;
}

.businessType.selected {
  box-shadow: 0 0 20px var(--primary);
}

.businessType-footer {
  position: relative;
  margin-top: 0px;
  font-weight: bold;
  color: black;  
  text-align: center;
  font-size: 9pt;
}

.businessType-footer-md {
  position: relative;
  margin-top: 0px;
  color: black;  
  font-size: 7pt;
  text-align: center;
  font-weight: bold;
}
.businessType-footer-xl {
  position: relative;
  margin-top: 0px;
  color: black;  
  font-size: 9pt;
  text-align: center;
  font-weight: bold;
}
.businessType-text {
  font-size: 14px;
  color: rgba(0, 0, 0, 0.7);
  font-weight: bold;
}

.businessType-author {
  font-size: 14px;
  color: #bab096;
  transition: color 0.3s;
}
</style>
