<template>

  <!-- <v-dialog v-model="model" :fullscreen="$vuetify.breakpoint.xsOnly" :content-class="$vuetify.breakpoint.xsOnly?'':'dialog-dims'" persistent>  -->
  <v-dialog v-model="model" fullscreen persistent>
    <v-card>
      <v-card-actions class="dialog-title linear-gradient" card dark>
        <v-card-title style="padding-left: 0!important;margin-left: 24px!important;">{{$t('message.vBusinessWizardModalHeader')}}</v-card-title>
        <v-spacer></v-spacer>
        <v-btn fab flat icon dark small
               style="margin-right: 24px!important;"
               @click="closeWizard()">
          <v-icon>close</v-icon>
        </v-btn>
      </v-card-actions>
      <v-layout row wrap>
        <v-flex xs12 md12 style="margin-left:5%!important;margin-right:5%!important">
          <!-- <vue-perfect-scrollbar :class="{'dialog-scroll':!$vuetify.breakpoint.xsOnly, 'dialog-full-scroll':$vuetify.breakpoint.xsOnly}" :settings="settings" > -->
          <v-stepper v-model="e6" vertical>
            <!-- BUSINESS TYPE -->
            <v-stepper-step :complete="stepBusinessType"
                            step="1"                                                   
                            :editable="stepBusinessType"
                            :color="currentBussinesClub.theme.primary">
              <strong>{{$t('message.vBusinessStepBusinessType')}}</strong>
            </v-stepper-step>
            <v-stepper-content :step="currentStep">
              <v-business-type :currentBusinessId="myBusinessSelected" :stepData="businessTypeInfo" @onContinue="saveBusinessType()"></v-business-type>
            </v-stepper-content>
            <!-- INITIAL DATA -->
            <v-stepper-step :complete="stepInitialData"
                            step="2"
                            :editable="stepInitialData"
                            :color="currentBussinesClub.theme.primary">
              <strong>{{$t('message.vBusinessStepBusinessInitialData')}}</strong>
            </v-stepper-step>
            <v-stepper-content step="2">
              <v-initial-data :currentBusinessId="myBusinessSelected"
                              :stepData="businessInitialDataInfo"
                              :businessTypeSelected="businessTypeSelected"
                              @onContinue="saveInitialData($event)"
                              @onGoBack="goBack($event)"></v-initial-data>
              <v-flex mb-5></v-flex>
            </v-stepper-content>
            <!-- MAIN ADDRESS -->
            <v-stepper-step v-if="businessSelected.hasLocale===1"
                            :complete="stepAddress"
                            :step="businessSelected.hasLocale===1?3:-1"
                            :editable="stepAddress"
                            :color="currentBussinesClub.theme.primary">
              <strong>{{$t('message.vBusinessStepBusinessAddMainAddress')}}</strong>
            </v-stepper-step>
            <v-stepper-content :step="nStepAddress">
              <v-address :currentBusinessId="myBusinessSelected"
                         :stepData="businessMainAddressInfo"
                         :defaultCountry="countryOperation"
                         @onContinue="saveMainAddress()"
                         @onGoBack="goBack($event)"></v-address>
              <v-flex mb-3></v-flex>
            </v-stepper-content>
            <!-- ADDITIONAL INFO -->
            <v-stepper-step :step="businessSelected.hasLocale===1?4:3"
                            :complete="stepAdditionalInfo"
                            :editable="stepAdditionalInfo"                          
                            :color="currentBussinesClub.theme.primary">
              <strong>{{$t('message.vBusinessStepBusinessAdditionalInfo')}}</strong>
            </v-stepper-step>
            <v-stepper-content :step="nStepAditionalInfo">
              <v-additional-info :currentBusinessId="myBusinessSelected"
                                 :stepData="businessAdditionalDataInfo"
                                 :hasLocale="hasLocale"
                                 @onContinue="saveAdditionalInfo()"
                                 @onGoBack="goBack($event)"></v-additional-info>
              <v-flex mb-3></v-flex>
            </v-stepper-content>
            <!-- FINAL DETAILS -->
            <v-stepper-step :step="businessSelected.hasLocale===1?5:4"
                            :editable="stepFinalDetails"
                            :complete="stepFinalDetails"
                            :color="currentBussinesClub.theme.primary">
              <strong>{{$t('message.vBusinessStepBusinessFinalDetails')}}</strong>
            </v-stepper-step>
            <v-stepper-content :step="nStepFinalDetails">
              <v-final-details :currentBusinessId="myBusinessSelected"
                               :stepData="businessFinalDetailsInfo"
                               @onContinue="finishSaveBusiness()"
                               @onGoBack="goBack($event)"></v-final-details>                            
              <v-flex mb-4></v-flex>
            </v-stepper-content>
          </v-stepper>
          <!-- </vue-perfect-scrollbar> -->
        </v-flex>
      </v-layout>
    </v-card>
    <v-confirm-dialog ref="closeWizardModal"
                      type="question"
                      heading="Salir del asistente"
                      v-bind:message="$t('message.discardChangesLabelBusiness')"
                      @onConfirm="confirmCloseWizard()"></v-confirm-dialog>
  </v-dialog>

</template>
<script>

  import Vue from 'vue'
  import { mapGetters } from 'vuex'
  import BusinessType from './businessType'
  import InitialData from './initialData'
  import Address from './address'
  import AdditionalInfo from './additionalInfo'
  import FinalDetails from './finalDetails' 
  import confirmDialog from 'Components/ySocialHome/confirmDialog'
  export default {
    components: {
      'v-business-type': BusinessType,
      'v-initial-data': InitialData,
      'v-address': Address,
      'v-additional-info': AdditionalInfo,
      'v-final-details': FinalDetails,     
      'v-confirm-dialog': confirmDialog
    },
    props: {
      idBusiness: String
    },
    data() {
      return {
        hasLocale: -1,
        isRegistering: false,
        countryOperation: null,
        businessTypeSelected: -1,
        businessStepper: null,
        businessTypeInfo: null,
        businessInitialDataInfo: null,
        businessMainAddressInfo: null,
        businessAdditionalDataInfo: null,
        businessFinalDetailsInfo: null,
        businessTypeInfo: null,
        myBusinessSelected: '',
        showStepBusinessType: 0,
        showStepInitialData: false,
        showStepAddress: false,
        showStepAdditionalInfo: false,
        showStepFinalDetails: false,
        currentStep: 1,
        nStepAddress: 3,
        nStepAditionalInfo: 3,
        nStepFinalDetails: 4,
        stepBusinessType: false,
        stepInitialData: false,
        stepAddress: false,
        stepAdditionalInfo: false,
        stepFinalDetails: false,
        model: false,
        settings: {
          maxScrollbarLength: 1024
        },
        e6: 1
      }
    },
    computed: {
      ...mapGetters([
        'userProfile',
        'countries',
        'provinces',
        'prefixes',
        'currentBussinesClub',
        'selectedLocale',
        'businessSelected'
      ]),
      userProfile: {
        get() {
          if (this.$store.getters.userProfile != '') return JSON.parse(this.$store.getters.userProfile)
        }
      }
    },
    mounted() {
      this.setWizardStep()
    },
    created() {
      this.initializeProps()
    },
    methods: {
      initializeProps() {
        this.myBusinessSelected = Vue.getObjectPath(this.businessSelected, 'id', "")
        this.businessStepper = Vue.getObjectPath(this.businessSelected, 'stepper', [])
        this.businessTypeInfo = Vue.getObjectPath(this.businessSelected, 'businessType', {})
        this.businessInitialDataInfo = Vue.getObjectPath(this.businessSelected, 'initialData', {})
        this.businessAdditionalDataInfo = Vue.getObjectPath(this.businessSelected, 'additionalInfo', {})
        this.businessMainAddressInfo = Vue.getObjectPath(this.businessSelected, 'addresses.addresses', [])
        this.businessFinalDetailsInfo = Vue.getObjectPath(this.businessSelected, 'finalDetails', {})
        this.businessTypeSelected = Vue.getObjectPath(this.businessSelected, 'initialData.businessType', -1)
        this.countryOperation = Vue.getObjectPath(this.businessSelected, 'initialData.operationCountry', '')
        this.hasLocale = Vue.getObjectPath(this.businessSelected, 'hasLocale', -1)   
      },
      setWizardStep() {
        this.model = true
        Vue.nextTick(() => {
          this.e6 = 1
          if (this.businessSelected.hasLocale == 1) {
            this.nStepAddress = 3
            this.nStepAditionalInfo = 4
            this.nStepFinalDetails = 5
          } else {
            this.nStepAditionalInfo = 3
            this.nStepFinalDetails = 4
            this.nStepAddress = 5
          }
          if (this.idBusiness) {
            let stepper = this.businessSelected.stepper
            for (let i = 0; i < stepper.length; i++) {
              if (stepper[i].status === 0) {
                switch (i) {
                  case 0:
                    this.e6 = 1
                    this.stepBusinessType = false
                    this.stepInitialData = false
                    this.stepAddress = false
                    this.stepAdditionalInfo = false
                    this.stepFinalDetails = false
                    break
                  case 1:
                    this.e6 = 2
                    this.stepBusinessType = true
                    this.stepInitialData = false
                    this.stepAddress = false
                    this.stepAdditionalInfo = false
                    this.stepFinalDetails = false
                    break
                  case 2:
                    if (this.businessSelected.hasLocale == 1) {
                      this.stepAddress = false
                      this.stepAdditionalInfo = false
                    } else {
                      this.stepAddress = false
                      this.stepAdditionalInfo = true
                    }
                    this.e6 = 3
                    this.stepFinalDetails = false
                    this.stepBusinessType = true
                    this.stepInitialData = true
                    break
                  case 3:
                    this.businessSelected.hasLocale == 1 ? (this.e6 = 4) : (this.e6 = 3)
                    this.stepBusinessType = true
                    this.stepInitialData = true
                    this.stepAddress = true
                    this.stepAdditionalInfo = false
                    this.stepFinalDetails = false
                    break
                  case 4:
                    this.businessSelected.hasLocale == 1 ? (this.e6 = 5) : (this.e6 = 4)
                    this.stepBusinessType = true
                    this.stepInitialData = true
                    this.stepAddress = true
                    this.stepAdditionalInfo = true
                    this.stepFinalDetails = false
                    break
                  default:
                    this.e6 = 1
                }
                break
              }
            }
          }
        })
      },
      confirmCloseWizard() {
        this.e6 = 1
        this.isRegistering = false
        this.showStepInitialData = false
        this.$refs.closeWizardModal.close()
        this.model = false
        this.$emit('onClose')
      },
      closeWizard() {
        this.$refs.closeWizardModal.openDialog()
      },
      goBack(data) {
        --this.e6
        this.initializeProps()
       // this.myBusinessSelected = this.businessSelected.id
      },
      saveBusinessType() {
        this.initializeProps()
        /* Update state of business type step to completed (100%) */
        this.updateStatusWizardStep(this.getStepID('businessType'), 1, 100, 2)
        this.stepBusinessType = true
      },
      saveInitialData(data) {
        this.initializeProps()        
        if (this.businessSelected.hasLocale == 1) {          
          this.isRegistering = true
          this.nStepAddress = 3
          this.nStepAditionalInfo = 4
          this.nStepFinalDetails = 5
        } else {
          this.isRegistering = false
          this.nStepAditionalInfo = 3
          this.nStepFinalDetails = 4        
          this.nStepAddress = 5
        }
        /* Update state of initial data step to completed (100%) */
        this.updateStatusWizardStep(this.getStepID('initialData'), 1, 100, 3)
        this.stepInitialData = true
      },
      saveMainAddress(data) {
        this.initializeProps() 
        /* Update state of main address step to completed (100%) */
        this.updateStatusWizardStep(this.getStepID('address'), 1, 100, 4)
        this.stepAddress = true
      },
      saveAdditionalInfo() {
        this.initializeProps() 
        if (this.businessSelected.hasLocale == 1) {
          this.nStepAddress = 3
          this.nStepAditionalInfo = 4
          this.nStepFinalDetails = 5
        } else {          
          this.nStepAddress = 5
          this.nStepAditionalInfo = 3
          this.nStepFinalDetails = 4
        }
        /* Update state of additonal info step to completed (100%) */
        this.updateStatusWizardStep(this.getStepID('aditionalInfo'), 1, 100, this.businessSelected.hasLocale == 1 ? 5 : 4)
        this.stepAdditionalInfo = true
      },
      finishSaveBusiness() {
        /* Update state of business. Only with all previuos information entered user can be in this
                                  view, so this is the step to set state to Created */
        this.updateStatusWizardStep(this.getStepID('finalDetails'), 1, 100, 1)
        this.$store
          .dispatch('doUpdateUserBusiness', {
            businessId: this.businessSelected.id,
            field: 'status',
            status: 2,
            path: 'status'
          })
          .then(() => {
            this.$store.dispatch('doNotification', {
              msg: 'Se ha registrado satisfactoriamente su negocio',
              type: 'success'
            })
            this.confirmCloseWizard()
          })
      },
      updateStatusWizardStep(step, status, progress, go) {
        this.$store
          .dispatch('doUpdateStatusWizardStep', {
            businessId: this.businessSelected.id,
            stepId: step,
            status: status,
            progress: progress
          })
          .then(() => {
            /* After update then continue to next step */
            this.e6 = go
          })
          .catch(() => {})
      },
      getStepID(stepName) {      
        return this.businessSelected.stepper.filter(s => s.step == stepName)[0]._id
      }
    }
  }

</script>
<style scope >
  .v-stepper__content {
    border-left: 1px solid var(--primary)!important;
  }
  @media only screen and (max-width: 600px) {
    .textalingMobile {
        margin-right: 12px !important;
        padding-left: 0px !important;
    }
    .mobileBotons {
        width: 100%;
    }
}

.mobileEditor {
    height: 60vh !important;
}

.swiper-pagination-bullet-custom {
    width: 16px;
    height: 16px;
    border-radius: 50%;
    margin: 3px;
}

.desktopEditor {
    height: 30vh !important;
}

.borderCards {
    border-style: solid;
    border-width: 1px;
    border-color: #cfd8dc;
}

.v-stepper__header {
    height: auto;
}

.paddingWizard {
    padding-left: 15%;
    padding-right: 15%;
    padding-top: 2%;
}

</style>
