<template>
  <v-hover>
    <v-card
      class="borderCards flexcard"
      :style="'height: 250px!important;'"
      slot-scope="{ hover }"
      :class="`elevation-${hover ? 12 : 2}`"
    >
      <v-container grid-list-md text-xs-center>
        <v-layout row wrap>
          <v-flex xs12 style="border-bottom: 3px double #8c8b8b;">
           <span class="textSkill spanColor spanEllipsis">
               {{getObjectPath(productsInfo[0], 'names.0.name',$t('message.businessNoName'))}}
           </span>    
          </v-flex>
          <v-flex  xs12 text-xs-left>
            <v-flex class="spanEllipsis">
              <div style="display:inline-block" class="pl-2">
                <span style="color:black">SKU: </span>
               <span style="color:black" class="spanColor">
                {{getObjectPath(productsInfo[0], 'codes.sku',$t('message.businessNoId'))}}
              </span>
            </div>       
            </v-flex>
            <v-flex>
              <div style="display:inline-block" class="pl-2">
                <span style="color:black">Stock: </span>
                <span style="color:black">200</span>
              </div>       
            </v-flex>
            <v-flex>
               <div style="display:inline-block" class="pl-2">
              <span style="color:black">{{$t('message.price')}}: $</span>
              <span style="color:black">1000</span>
            </div>
            </v-flex> 
                <v-flex>
              <span style="color:black">
                <v-icon :color="currentBussinesClub.theme.primary">mdi-shopify</v-icon>En creación
              </span>
            </v-flex> 
              <h5 style="color:black">
                <i></i>
              </h5>
            </v-flex> 
        </v-layout>
      </v-container>
      <v-card-actions>
        <div style="position: absolute!important;right: 10px!important; bottom:5px!important;">
          <v-speed-dial
            v-model="fab"
            direction="top"
            open-on-hover
            transition="slide-y-reverse-transition"
          >
            <template v-slot:activator>
              <v-btn v-model="fab" :color="currentBussinesClub.theme.primary" dark fab small  style="margin-top:10px!important">
                <v-icon>mdi mdi-plus</v-icon>
                <v-icon>mdi mdi-close</v-icon>      
              </v-btn>
            </template>

            <v-btn
              v-if="getObjectPath(productsInfo[0], 'status',0)==1"
              @click="continueWizard(productsInfo[0]._id)"
              :color="currentBussinesClub.theme.secondary"
              :title="$t('message.vBusinessDoContinueWizard')"
              fab
              dark
              small
              style="margin-top:10px!important;"
            >
              <v-icon>mdi mdi-assistant</v-icon>
            </v-btn>
            <v-btn     
              :color="currentBussinesClub.theme.secondary"
              :title="$t('message.vBusinessDoManage')"
              fab
              dark
              small
              style="margin-top:20px!important;"
              @click="openProductManagement"
            >
              <v-icon>mdi mdi-settings</v-icon>
            </v-btn>
              <v-btn
              v-if="getObjectPath(productsInfo[0], 'status',0)==1"
              :color="currentBussinesClub.theme.primary"
              :title="$t('message.vProductDelete')"
              fab
              dark
              small
              style="margin-top:10px!important;"
            >
              <v-icon>mdi mdi-trash-can</v-icon>
            </v-btn>
          </v-speed-dial>
        </div>
      </v-card-actions>
      <v-product-management
        ref="productManagement"
        :itemProduct.sync="productsInfo[0]"
        @onClose="openConfirmCloseDialog"
      ></v-product-management>
    </v-card>
  </v-hover>
</template>

<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import appConfig from "Constants/AppConfig";
import productManagement from "./productManagement";

export default {
  props: {
    productsInfo: [Array]
  },
  components: {
    "v-product-management": productManagement
  },
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
  mounted() {
    
  },
  created() {
  },
  created() {},
  methods: {
    openProductManagement() {
      this.$root.$children[0].$emit(
        "showLoading",
        "Cargando información del producto"
      );
      this.$store
        .dispatch("doGetProductInformation", {
          product_id: this.getObjectPath(this.productsInfo[0], "_id", "")
        })
        .finally(() => {
          this.$root.$children[0].$emit("hideLoading");
          this.$refs.productManagement.openDialog();
        });
    },
    openConfirmCloseDialog() {
      this.$refs.productManagement.openConfirmDialog();
    },
  
    continueWizard(id){
      this.$emit("onContinue",id)
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
  .spanColor{
    color: black;
  }
  .spanEllipsis{
     text-overflow: ellipsis!important;

  /* Required for text-overflow to do anything */
  white-space: nowrap !important;
  overflow: hidden !important;
  }
</style>
 