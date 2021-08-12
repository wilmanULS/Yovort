<template>

  <div>
    <!-- Information for user has not businesses -->
    <v-layout class="no-padding padding-top-24">
      <v-flex xs12 sm6 offset-sm3 v-if="!userBusinesses[0]&&!isManageBusiness">
        <v-card  >
          <v-card-title primary-title>
            <v-layout align-center justify-center column fill-height style="align-self: flex-end">
              <v-flex></v-flex>
              <v-flex style="flex-grow:1;">&nbsp;</v-flex>
              <v-flex style="text-align:center;">
               <v-img
                  :src="`${FMS_URL}/image/fetch/36d1b8e8-ada7-4713-9464-e6ed70878106.png`"               
                  class="rounded-circle img-responsive"
                  style="height:100%!important"
                >  </v-img>  
              </v-flex>
              <v-flex mt-3 style="text-align:center;">
                <h1 style="font-size: 250%; font-color:#808080">{{$t('message.vBusinessUserNoBusinesses')}}</h1>
              </v-flex>
              <v-flex mt-3 style="text-align:center;padding-left:10%;padding-right:10%;">
                <h4 class="grey--text"
                    style=" line-height: 1.5em;">{{$t('message.vBusinessNoBusinessesDescription')}}</h4>
              </v-flex>
              <v-flex mt-3>
                <v-layout row wrap>
                  <v-flex class="pa-2">
                    <v-btn :color="currentBussinesClub.theme.primary" dark large block
                           style="border: 1px; padding: 10px; box-shadow: 5px 10px 18px #888888;"
                           @click="startWizardCreation()">{{$t('message.vBusinessCreateNew')}}</v-btn>
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
    </v-layout>
    <!--User's businesses list-->
    <v-layout row wrap v-if="userBusinesses[0]&&!isManageBusiness">
      <v-flex xs12 md12>
        <v-card-text>
          <v-container grid-list-lg class="no-padding">
            <v-layout row wrap>
              <!-- Business creation card -->
              <v-flex xs12 sm12 md4 lg3 xl3>
                <v-card height="300px" class="cardAdd borderCards" style="cursor:pointer"  @click="startWizardCreation()" >
                  <v-container class="no-padding">
                    <v-layout row wrap>
                      <v-flex xs12 text-xs-center class="center">
                        <div>
                          <v-btn small
                                 :color="currentBussinesClub.theme.primary"
                                 fab
                                 dark
                                 >
                            <v-icon>mdi mdi-plus</v-icon>
                          </v-btn>
                        </div>
                        <span class="infoCardText">{{$t('message.vBusinessCreateNew')}}</span>
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-card>
              </v-flex>
              <!--Businesses cards -->
              <v-flex xs12 sm12 md4 lg3 xl3  v-for="(business,index) in userBusinesses" :key="index">
                <v-business :businessInfo="business"
                            @onDoManage="manageBusiness($event)"
                            @onDeleteBusiness="confirmDelete($event)"
                            @onWizard="continueSetUp($event)"></v-business>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
      </v-flex>
    </v-layout>
    <!--Dialog modal for confirm business deleting action-->
    <v-confirm-dialog 
                      ref="confirmDelete"
                      type="question"
                      :heading="$t('message.vBusinessDelete')"
                      v-bind:message="$t('message.deleteBusinessConfirmation',{business:  this.nameBusinessSelected})"
                      @onConfirm="deleteBusinessById()"></v-confirm-dialog>
    <!--Control panel component-->
    <v-business-control-panel v-if="isManageBusiness" :infoBusiness="infoBusiness" @onClose="closeManageBusiness()"></v-business-control-panel>
    <!--Business wizard creation component-->
    <v-business-wizard v-if="showWizard" :idBusiness="myBusinessSelected" @onClose="closeWizardModal()"></v-business-wizard>
  </div>

</template>
<script>

  import Vue from 'vue'
  import { mapGetters } from 'vuex'
  import Business from './business'
  import router from '../../router'
  import { EventBus } from '../../eventBus.js'
  import confirmDialog from 'Components/ySocialHome/confirmDialog'
  import Wizard from './wizard'
  import ControlPanel from './controlPanel'
import appConfig from "Constants/AppConfig";
  export default {
    components: {
      'v-business': Business,
      'v-confirm-dialog': confirmDialog,
      'v-business-wizard': Wizard,
      'v-business-control-panel': ControlPanel,
      'v-confirm-dialog': confirmDialog
    },
    data() {
      return {
          FMS_URL: appConfig.FMS_URL,
        showWizard: false,
        isManageBusiness: false,
        hasBusiness: false,
        e6: 1,
        viewReady: false,
        deleteID: -1,
        nameBusinessSelected: '',
        myBusinessSelected: '',
        infoBusiness:{}
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
        'userBusinesses',
        'businessSelected'
      ]),
      userProfile: {
        get() {
          if (this.$store.getters.userProfile != '') return JSON.parse(this.$store.getters.userProfile)
        }
      }
    },
    created() {
      Vue.nextTick(() => {
        this.getUserBusiness()
        this.$store.dispatch('getAllStores')
      })
    },    
    methods: {
      /* Get user's businesses list */
      getUserBusiness() {
        this.$root.$children[0].$emit('showLoading', 'Cargando negocios del usuario')
        this.$store
          .dispatch('doGetUserBusinesses')
          .catch(() => {
            this.$store.dispatch('doNotification', {
              msg: this.$t('message.couldNotGetUserBusinesses'),
              type: 'error'
            })
          })
          .finally(() => {
            this.$root.$children[0].$emit('hideLoading')
          })
      },
      /* Show control panel for selected business */
      manageBusiness(data) {
        this.infoBusiness=data;
        /* Set current selected business id on vuex state for management on control panel */
        this.$store.dispatch('doSetCPBusinessId', data.id).then(() => {
          this.isManageBusiness = true
        })
      },
      /* Close control panel view*/
      closeManageBusiness() {
        this.isManageBusiness = false
        this.getUserBusiness();
      },
      /* This triggered on a new business creation */
      startWizardCreation() {
          this.$store
          .dispatch('doValidateBusinessCreation')
          .then((res) => {          
            this.$root.$children[0].$emit('showLoading', 'Preparando asistente')
               this.myBusinessSelected = ''
               this.$store
              .dispatch('doClearBusinessSelected')
                .then(() => {
                  this.showWizard = true
                })
                .finally(() => {
                  this.$root.$children[0].$emit('hideLoading')
                })
          }).catch((error)=>{
           
             /* Notify that user can not create a business until finish creation of current on edition*/
            this.$store.dispatch('doNotification', {
              msg: this.$t('message.vBusinessCreateNewFail'),
              type: 'error'
            })
          }
            
          )
        
      },
      /* This triggered when a business is NOT finished at all */
      continueSetUp(id) {
        this.$root.$children[0].$emit('showLoading', 'Obteniendo información del negocio')
        this.myBusinessSelected = id
        this.$store
          .dispatch('doGetUserBusinessById', { businessId: id })
          .then(res => {
            /* Get list of categories based on business model */
            let model = Vue.getObjectPath(this.businessSelected, 'additionalInfo.businessModel.model', -1)           
            if (model !== -1) {
              let categoryName = ''
              switch (model) {
                case 1:
                  categoryName = 'products'
                  break
                case 2:
                  categoryName = 'services'
                  break
                default:
                  categoryName = 'all'
                  break
              }
              this.$store
                .dispatch('doGetBusinessCategories', {
                  category: categoryName
                })
                .finally(() => {
                  this.showWizard = true
                })
            } else this.showWizard = true
          })
          .finally(() => {
            this.$root.$children[0].$emit('hideLoading')
          })
      },
      /* This is called only when user has finished successfully creation of business */
      closeWizardModal() {
        this.modalWizard = false
        this.getUserBusiness()
        EventBus.$emit('clearWizard')
        this.showWizard = false
      },
      /* Open dialog modal for confirm deleting business action */
      confirmDelete(data) {
        this.deleteID = data.id
        this.nameBusinessSelected = data.name
        this.$refs.confirmDelete.openDialog()
      },
      /* Delete the specified user business, then on success get the updated user business list */
      deleteBusinessById() {
        this.$store
          .dispatch('doDeleteUserBusiness', { businessId: this.deleteID })
          .then(() => {
            /* Notify that business was deleted successfully */
            this.$store.dispatch('doNotification', {
              msg: this.$t('message.businessDeletedSuccess'),
              type: 'success'
            })
            /* Get user's business list */
            this.getUserBusiness()
          })
          .catch(() => {
            this.$store.dispatch('doNotification', {
              msg: this.$t('message.couldNotDeleteUserBusiness'),
              type: 'error'
            })
          })
          .finally(() => {
            /* Close dialog modal confirmation*/
            this.$refs.confirmDelete.close()
            this.deleteID = -1
            this.nameBusinessSelected = ''
          })
      }
    }
  }

</script>
  <style scope>
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

.cardAdd {
    border: dashed 2px gray !important;
}

.infoCardText {
    color: gray;
    font-size: 12px !important;
    text-transform: capitalize !important;
    font-weight: 700 !important;
}

.textSkill {
    font-size: 19px !important;
    text-transform: capitalize !important;
    font-weight: 700 !important;
}

.center {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    -webkit-transform: translate(-50%, -50%);
}

</style> 
