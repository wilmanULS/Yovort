<template>
  <!-- <v-dialog v-model="model" :fullscreen="$vuetify.breakpoint.xsOnly" :content-class="$vuetify.breakpoint.xsOnly?'':'dialog-dims'" persistent>  -->
  <v-dialog
    v-model="model"
    fullscreen
    persistent
    lazy
  >
    <v-card>
      <v-card-actions class="dialog-title linear-gradient" card dark>
        <v-card-title
          style="padding-left: 0!important;margin-left: 24px!important;"
        >{{$t('message.createNewProduct')}}</v-card-title>
        <v-spacer></v-spacer>
        <v-btn
          fab
          flat
          icon
          dark
          small
          style="margin-right: 24px!important;"
          @click="OpenConfirmCloseDialog()"
        >
          <v-icon>close</v-icon>
        </v-btn>
      </v-card-actions>
      <v-layout row wrap>
        <v-flex xs12 md12 style="margin-left:5%!important;margin-right:5%!important">
          <!-- <vue-perfect-scrollbar :class="{'dialog-scroll':!$vuetify.breakpoint.xsOnly, 'dialog-full-scroll':$vuetify.breakpoint.xsOnly}" :settings="settings" > -->
          <v-stepper v-model="e6" vertical v-if="model">
            <!-- BUSINESS TYPE -->
            <v-stepper-step  
              step="1"
              :complete="stepInitalData"
              :color="currentBussinesClub.theme.primary"
            >
              <strong>{{$t('message.vBusinessStepBusinessInitialData')}}</strong>
            </v-stepper-step>
            <v-stepper-content step="1">
                 <v-product-initial-data ref="initialData" @onContinue="saveInitialData($event)" @onGoBack="GoBack($event)" ></v-product-initial-data>
                
            </v-stepper-content>

             <v-stepper-step
              step="2"
              :complete="stepProductDetail"
              :color="currentBussinesClub.theme.primary"
            >
            <strong>{{$t('message.vProductDetailProduct')}}</strong>
            </v-stepper-step>
            <v-stepper-content step="2">
               <v-product-detail @onContinue="saveProductDetail($event)" @onGoBack="GoBack($event)" ></v-product-detail>
            </v-stepper-content>
             <v-stepper-step
              step="3"
              :complete="stepPoliticWarranty"
              :color="currentBussinesClub.theme.primary"
            >
            <strong>{{$t('message.vProductPoliticsWarranty')}}</strong>
            </v-stepper-step>
            <v-stepper-content step="3">
                <v-product-warranty @onContinue="saveProductWarranty($event)" @onGoBack="GoBack($event)" ></v-product-warranty>
            </v-stepper-content> 
             <v-stepper-step
              step="4"
              :complete="stepPoliticdelivery"
              :color="currentBussinesClub.theme.primary"
            >
            <strong>{{$t('message.vProductPoliticsDelivery')}}</strong>
            </v-stepper-step>
            <v-stepper-content step="4">
                <v-ptoduct-delivery @onContinue="saveProductDelivery($event)" @onGoBack="GoBack($event)" ></v-ptoduct-delivery>
            </v-stepper-content>    
          </v-stepper>
          <!-- </vue-perfect-scrollbar> -->
        </v-flex>
      </v-layout>
    </v-card>
    <v-confirm-dialog
      ref="closeWizardModal"
      type="question"
      heading="Salir del asistente"
      v-bind:message="'¿Está seguro de salir del asistente para la creación de productos?'"
      @onConfirm="ConfirmCloseWizard()"
    ></v-confirm-dialog>
  </v-dialog>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import confirmDialog from "Components/ySocialHome/confirmDialog";
import productType from "./steperContentProduct/productType";
import productInitialData from "./steperContentProduct/productInitialData";
import productDetail from "./steperContentProduct/productDetail";
import productPoliticWarranty from "./steperContentProduct/productPoliticWarranty";
import productPoliticDelivery from "./steperContentProduct/productPoliticDelivery";

export default {
   components: {
    "v-confirm-dialog": confirmDialog,
    "v-product-type":productType,
    "v-product-initial-data":productInitialData,
    "v-product-detail":productDetail,
    "v-product-warranty":productPoliticWarranty,
    "v-ptoduct-delivery":productPoliticDelivery

  }, 
  computed: {
    ...mapGetters([
      "userProfile",
      "currentBussinesClub",  
      "getSelectedProductInformation",
      "currentStep",
      "userProducts"
    ]),
    currentUserBusiness: {
      get() {
        if (this.$store.getters.currentUserBusiness != "")
          return this.$store.getters.currentUserBusiness;
      }
    }
  },
  mounted() {},
  created() {
    this.getDataSteper()
  },
  methods: {
    getDataSteper(){
      console.log("datadel producto",this.getSelectedProductInformation);
     this.currentSelected=this.getObjectPath(this.getSelectedProductInformation,"currentStepper",1);
     this.e6= this.currentSelected
    },
    currentStepper(step){
     let dataCurrent={
       productData:{
         productId: this.userProducts._id,
         currentStepper:step
       },
       type:"currentStepper"
     }
     console.log(dataCurrent)
   this.$store.dispatch("doUpdateCurrentStep",dataCurrent)
    },
    saveInitialData(){ 
    this.currentStepper(1)
    this.stepInitalData=true;
       this.e6 = 2;
    },
    saveProductDetail(){
    this.currentStepper(2)
    this.stepProductDetail=true;
        this.e6 = 3;
    },
    saveProductWarranty(){
      this.currentStepper(3);
      stepPoliticWarranty=true;
     
       this.e6 = 4;
    },
     saveProductDelivery(){
         this.currentStepper(4);
        this.stepPoliticdelivery=true
          let statusData={
          productData:{
            productId:this.userProducts._id,
            status:2
          },
          type:"Status"
        }
      this.$store.dispatch("doUpdateStatus",statusData).then(()=>{
        this.model=false;
      })   
    },
      GoBack(data) {
          this.currentStepper(data);
      --this.e6;
    },
    /**Open for modal*/
    Open() {
      /*  this.e6=4; */
      
      this.model = true;
     /*  (this.stepBusinessType = false),
        (this.stepInitialData = false),
        (this.stepAddress = false),
        (this.stepAdditionalInfo = false),
        (this.stepFinalDetails = false); */
    },
    ReloadData(){
      console.log('Reload')
      this.$refs.initialData.reloadFields()
    },
    ConfirmCloseWizard() {
      this.e6 = 1;
      this.$refs.closeWizardModal.close();
      this.model = false;
      this.$emit("onClose");
    },
    OpenConfirmCloseDialog() {
       this.$refs.closeWizardModal.openDialog(); 
    },    
  },
  data() {
    return {
      stepInitalData:false,
      stepProductDetail:false,
      stepPoliticWarranty:false,
      stepPoliticdelivery:false,
      model: false,
      e6: 1,
      currentSelected:null,
      getObjectPath: Vue.getObjectPath,
    };
  }
};
</script>
<style>
</style>
