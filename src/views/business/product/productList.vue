<template>
  <div>
    <!--     <v-layout class="no-padding padding-top-24">
      <v-flex xs12 sm12 v-if="JSON.stringify(allUserProducts)=='{}'" >
        <v-card class="borderCards">  
          <v-card-title primary-title>
            <v-layout align-center justify-center column fill-height style="align-self: flex-end">
              <v-flex></v-flex>
              <v-flex style="flex-grow:1;">&nbsp;</v-flex>
              <v-flex style="text-align:center;">
                <img
                  src="https://banner2.kisspng.com/20180611/lcg/kisspng-computer-icons-desktop-wallpaper-product-marketing-5b1e5b9ba64743.4743842715287161876811.jpg"
                  class="rounded-circle img-responsive"
                  width="102"
                  height="102"
                >
              </v-flex>
              <v-flex mt-3 style="text-align:center;">
                <h1 style="font-size: 250%; font-color:#808080">{{$t('message.vProductNoProduct')}}</h1>
              </v-flex>
              <v-flex mt-3 style="text-align:center;padding-left:10%;padding-right:10%;">
                <h4
                  class="grey--text"
                  style=" line-height: 1.5em;"
                >{{$t('message.vProductNoProductsDescription')}}</h4>
              </v-flex>
              <v-flex mt-3>
                <v-layout row wrap>
                  <v-flex class="pa-2">
                    <v-btn
                      :color="currentBussinesClub.theme.primary"
                      dark
                      block
                      class="rounded-buttons"
                      @click="openWizardProduct()"
                    >{{$t('message.newProduct')}}</v-btn>
                  </v-flex>
                </v-layout>
                <v-layout row wrap>
                  <v-flex class="pa-4"></v-flex>
                </v-layout>
              </v-flex>
            </v-layout>
          </v-card-title>
        </v-card>
      </v-flex>
    </v-layout>-->
    <v-layout row wrap>
      <v-flex xs12 md12>
        <v-card-text>
          <v-container grid-list-lg class="no-padding">
            <v-layout row wrap>
              <v-flex xs12 md4>
                <v-card height="250px" class="cardAdd borderCards">
                  <v-container class="no-padding">
                    <v-layout row wrap>
                      <v-flex xs12 text-xs-center class="center">
                        <div>
                          <v-btn
                            small
                            :color="currentBussinesClub.theme.primary"
                            fab
                            dark
                            @click="openWizardProduct()"
                          >
                            <v-icon>mdi mdi-plus</v-icon>
                          </v-btn>
                        </div>
                        <span class="infoCardText">{{$t('message.createNewProduct')}}</span>
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-card>
              </v-flex>
              <v-flex xs12 md4 v-for="(products,index) in allUserProducts" :key="index">
                <v-products :productsInfo="products" @onContinue="continueWizard($event)"></v-products>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
      </v-flex>
    </v-layout>
    <v-business-wizard ref="wizard" @onClose="closeWizardProduct"></v-business-wizard>
  </div>
</template>
<<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import WizardProduct from "../product/wizardProduct"
import Products from "../product/products"
export default {
    components:{
        "v-business-wizard":WizardProduct,
        "v-products":Products
      
    },
      computed: {
    ...mapGetters([
      "currentBussinesClub",
      "userProducts",
      "allUserProducts",
      "businessId",
      "userProducts"
    ]),
  },
  data(){
    return{
     showWizard:false
    }
  },
  created(){
     
    this.GetUserProducts();
  },
    methods: {
      /**continue creation product in the wizard */
      continueWizard(id){
        this.$store.dispatch("doGetProductInformation",{product_id:id}).then(res=>{
          this.$refs.wizard.Open();
          this.$store.commit("mSetUserProducts",res.data.data[0][0]);
        })
      },
      updateStatus(){
     
        let statusData={
          productData:{
            productId:this.userProducts._id,
            status:1
          },
          type:"Status"
        }
      this.$store.dispatch("doUpdateStatus",statusData)
      },

      /**create new product in the business */
        openWizardProduct(){
           this.$store.dispatch("doCreateUserProducts", { businessId: this.businessId })
          .then(res => {
               this.$refs.wizard.Open();
               this.updateStatus()
               
            this.$store.dispatch("doGetUserProducts",this.businessId)
          }).catch(err => {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.couldNotAddUserBusiness"),
              type: "error"
            });
          }).finally(()=>{
            this.$store.commit('mSetProductSelected',{})
          })
        },
        closeWizardProduct(){
        this.$refs.wizard.OpenConfirmCloseDialog();        
        },
        /**get all products by business Id */
          GetUserProducts() {        
      	this.$root.$children[0].$emit(
				"showLoading",
				"Cargando productos del negocio"
			);
      this.$store
        .dispatch("doGetUserProducts",this.businessId)
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.vProductNoProductsUser"),
            type: "error"
          });
        })
        .finally(() => {
            this.$root.$children[0].$emit("hideLoading");
        });
    },
    },
}
</script>
<style scope>
</style>