<template>
  <v-form ref="form" v-model="valid" v-if="viewIsReady">
    <v-flex>
      <v-layout row wrap style="heigth: 100%!important;">
        <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
          <v-text-field ref="name"
                        :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
                        clearable
                        v-model="name"
                        hide-details
                        :rules="nameRules"
                        :color="currentBussinesClub.theme.primary"
                        v-bind:label="$t('message.commercialName')"
                        required
                        @change="updateField('name',name,'identity.name')"></v-text-field>
        </v-flex>
        <v-flex xs12 md6
                :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}"
                v-if="businessTypeSelected!=1">
          <v-text-field :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
                        ref="businessName"
                        v-model="businessName"
                        hide-details
                        clearable
                        required
                        :rules="nameRules"
                        :color="currentBussinesClub.theme.primary"
                        v-bind:label="$t('message.socialReason')"
                        @change="updateField('businessName',businessName,'identity.businessName')"></v-text-field>
        </v-flex>
        <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
          <v-autocomplete ref="creationCountry"
                          :no-data-text="$t('message.nodatafound')"
                         
                          :items="countries"
                          item-text="name"
                          item-value="countryId"
                          :color="currentBussinesClub.theme.primary"
                          v-bind:label="$t('message.vBusinessStepBusinessInitialDataCountryCreation')"
                          hide-details
                          v-model="creationCountry"
                          attach
                          required
                          :rules="nameRules"
                          :loading="loadingCountries"
                          :class="{'vb-ml': $vuetify.breakpoint.mdAndUp&&businessTypeSelected===1,'vb-mr': $vuetify.breakpoint.mdAndUp&&businessTypeSelected!=1 , 'mb-1':$vuetify.breakpoint.smAndDown }"

                          @change="updateField('creationCountry',creationCountry,'identity.creationCountry')">
            <template slot="selection" slot-scope="data">
                    <span>
                      <flag :iso="data.item.flag"/>
                      {{ data.item.name }}
                   </span>
                  </template>
            <template slot="item" slot-scope="data">
                    <v-list-tile-content>
                      <v-list-tile-title>
                        <flag :iso="data.item.flag"/>
                        {{data.item.name}}
                      </v-list-tile-title>
                    </v-list-tile-content>
                  </template>
          </v-autocomplete>
        </v-flex>
        <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
          <v-autocomplete ref="operationCountry"
                          :no-data-text="$t('message.nodatafound')"                         
                          :items="countries"
                          item-text="name"
                          item-value="countryId"
                          :color="currentBussinesClub.theme.primary"
                          v-bind:label="$t('message.vBusinessStepBusinessInitialDataCountryOperation')"
                          hide-details
                          v-model="operationCountry"
                          attach
                          required
                          :rules="nameRules"
                          :loading="loadingCountries"
                          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp&&businessTypeSelected===1,'vb-ml': $vuetify.breakpoint.mdAndUp&&businessTypeSelected!=1 , 'mb-1':$vuetify.breakpoint.smAndDown }"

                          @change="updateField('operationCountry',operationCountry,'identity.operationCountry')">
            <template slot="selection" slot-scope="data">
                    <span>
                      <flag :iso="data.item.flag"/>
                      {{ data.item.name }}
                   </span>
                  </template>
            <template slot="item" slot-scope="data">
                    <v-list-tile-content>
                      <v-list-tile-title>
                        <flag :iso="data.item.flag"/>
                        {{data.item.name}}
                      </v-list-tile-title>
                    </v-list-tile-content>
                  </template>
          </v-autocomplete>
        </v-flex>
        <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
          <v-menu ref="menu"
                  v-model="menu"
                  :close-on-content-click="false"
                  :nudge-right="40"
                  lazy
                  transition="scale-transition"
                  offset-y
                  full-width
                  min-width="290px">
            <v-text-field :class="{'vb-ml': $vuetify.breakpoint.mdAndUp&&businessTypeSelected===1,'vb-mr': $vuetify.breakpoint.mdAndUp&&businessTypeSelected!=1 , 'mb-1':$vuetify.breakpoint.smAndDown }"

                          ref="creationDate"
                          class="mr05"
                          readonly
                          :color="currentBussinesClub.theme.primary"
                          slot="activator"
                          v-model="creationDate"
                          v-bind:label="$t('message.creationDate')"
                          hide-details
                          required
                          :rules="nameRules"
                          append-icon="event"></v-text-field>
            <v-date-picker :first-day-of-week="1"
                           :locale="selectedLocale.locale"
                           :color="currentBussinesClub.theme.primary"
                           v-model="creationDate"
                           scrollable
                           min="1850-01-01"
                           :max="calculateMax()">
              <v-spacer></v-spacer>
             <!--  <v-btn flat
                     :color="currentBussinesClub.theme.secondary"
                     @click="menu = false;">{{$t('message.cancel')}}</v-btn>
              <v-btn flat
                     :color="currentBussinesClub.theme.primary"
                     @click="$refs.menu.save(creationDate);">{{$t('message.ok')}}</v-btn> -->
            </v-date-picker>
          </v-menu>
        </v-flex>
        <v-flex xs12 md6
                :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}"
                v-if="businessTypeSelected!=1">
          <v-text-field ref="identification"
                        :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
                        clearable
                        v-model="identification"
                        hide-details
                        :color="currentBussinesClub.theme.primary"
                        v-bind:label="$t('message.unicIdentificationNumber')"
                        required
                        :rules="nameRules"
                        @change="updateField('identification',identification,'identity.identification')"></v-text-field>
        </v-flex>
        <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
          <v-select ref="businessModel"
                    :color="currentBussinesClub.theme.primary"
                    :items="businessModel"
                    :label="$t('message.businessModel')"
                    v-model="businessModelSelected"
                    :loading="businessModelLoading"
                    attach
                    item-text="name"
                    item-value="code"
                    hide-details
                    @change="saveBusinessModel(businessModelSelected)"
                    :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
                    required
                    :rules="nameRules"></v-select>
        </v-flex>
        <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
          <v-select ref="hasLocale"
                    :color="currentBussinesClub.theme.primary"
                    :items="confirmationAnswers"
                    :label="$t('message.vBusinessStepBusinessInitialDataHasLocale')"
                    v-model="hasLocale"
                    :loading="hasLocaleLoading"
                    attach
                    item-text="name"
                    item-value="code"
                    hide-details
                    :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
                    required
                    :rules="nameRules"
                    @change="updateField('hasLocale',hasLocale,'hasLocale')"></v-select>
        </v-flex>

        <v-flex text-xs-left text-md-left pl-1 xs12>
          <span style="font-weight:bold!important" class="requeridTitles">* {{$t('message.requiredFields')}}</span>
        </v-flex>

        <v-layout mt-5>
          <!--   <v-flex mb-5></v-flex> -->

          <v-btn style="margin-left:0px"
                 :color="currentBussinesClub.theme.primary"
                 dark
                 @click="saveInitialData()">{{$t('message.vBusinessStepBusinessContinue')}}</v-btn>
          <v-btn @click="goBack(1)" flat :color="currentBussinesClub.theme.secondary">{{$t('message.vBusinessStepBusinessBack')}}</v-btn>
        </v-layout>
      </v-layout>
    </v-flex>
  </v-form>

</template>
<script>

  import Vue from 'vue'
  import { mapGetters } from 'vuex'
  import AppConfig from 'Constants/AppConfig'
  import confirmDialog from '../../components/ySocialHome/confirmDialog'
  import { EventBus } from '../../eventBus.js'
  export default {
    components: {
      'v-confirm-dialog': confirmDialog
    },
    props: {
      currentBusinessId: String,
      stepData: Object,
      businessTypeSelected: Number
    },
    data() {
      return {
        businessType: null,
        valid: false,
        viewIsReady: false,
        loadingCountries: false,
        businessModelLoading: false,
        hasLocaleLoading: false,
        model: null,
        menu: false,
        /* Data use as fields for v-model*/
        name: '',
        businessName: '',
        creationCountry: null,
        operationCountry: null,
        identification: '',
        creationDate: '',
        businessModelSelected: null,
        hasLocale: null,
        /* Validation rules */
        nameRules: '',
        required: ''
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
        'businessModel',        
        'confirmationAnswers',
        'businessCategoriesList'
      ]),
      userProfile: {
        get() {
          if (this.$store.getters.userProfile != '') return JSON.parse(this.$store.getters.userProfile)
        }
      }
    },
    watch: {
      creationDate(val) {
        if (val) {
          this.updateField('creationDate', val, 'identity.creationDate')
        }
      }
    },
    created() {
      EventBus.$on('clearWizard', () => {
        this.clearData()
      })
      this.required = this.$t('message.required')
      this.nameRules = [v => !!v || this.required]
      this.businessType = Vue.getObjectPath(this.stepData, 'businessType.businessType', -1)
      /* Load catalogs lists for this view */
      Vue.nextTick(() => {
        this.loadingCountries = true
        this.$store.dispatch('doGetCountries').finally(() => {
          this.loadingCountries = false
        })
        /* Get items for answers confirmation catalog */
        this.hasLocaleLoading = true
        this.$store
          .dispatch('doGetAnswersConfirmation')
          .then(() => {
            /* Get items for business model catalog */
            this.businessModelLoading = true
            this.$store.dispatch('doGetBusinessModel').finally(() => {
              this.businessModelLoading = false
              this.viewIsReady = true
            })
          })
          .finally(() => {
            this.hasLocaleLoading = false
          })
      })
    }, 
    mounted() {
      this.reloadFields()
    },
    methods: {
      /* Set fields with data coming on stepData prop */
      reloadFields() {
        this.name = Vue.getObjectPath(this.stepData, 'name', '')
        this.businessName = Vue.getObjectPath(this.stepData, 'businessName', '')
        this.creationCountry = Vue.getObjectPath(this.stepData, 'creationCountry', null)
        this.creationDate = Vue.getObjectPath(this.stepData, 'creationDate', '')
        this.operationCountry = Vue.getObjectPath(this.stepData, 'operationCountry', null)
        this.identification = Vue.getObjectPath(this.stepData, 'identification', '')
        this.businessModelSelected = Vue.getObjectPath(this.stepData, 'businessModel.model', null)
        if (this.businessModelSelected === -1) {
          this.businessModelSelected = null
        }
        this.hasLocale = Vue.getObjectPath(this.stepData, 'hasLocale', null)
        this.businessType = Vue.getObjectPath(this.stepData, 'businessType.businessType', -1)
      },
      /* Update dinamically the value of a property */
      updateField(field, value, path) {
        /* Validate if field is not empty */
        if (this[field] != '') {
          let data = {
            businessId: this.currentBusinessId,
            field: field,
            path: path
          }
          data[field] = value
          this.$store.dispatch('doUpdateUserBusiness', data).catch(() => {
            this.$store.dispatch('doNotification', {
              msg: this.$t('message.couldNotUpdateUserBusinessInfo'),
              type: 'error'
            })
          })
        }
      },
      /* Triggered on businessModel selection change*/
      saveBusinessModel(id) {     
        let categoryName = ''
        switch (id) {
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
        /* Prepare insert/update data for businessModel*/
        let businessModelData = {
          /* Set value from current data object */
          businessId:this.currentBusinessId,
          model: this.businessModelSelected,        
        }
        /* Check if there is business model is already inserted on db*/
        let modelId = Vue.getObjectPath(this.stepData, 'businessModel._id', null);
        if(modelId){
          /* Add modelId to update */
          businessModelData['modelId']=modelId;
          businessModelData['type']='model';
        }      
        this.$store
          .dispatch('doRegisterBusinessModel', businessModelData)
          .then(() => {
            /* After update then retrieve business categories to recover on additional data step*/
            this.$store.dispatch('doGetBusinessCategories', {
              category: categoryName
            }).then(()=>{
              
            })
            .catch(()=>{              
              //notify error
            }) 
          })
          .catch(() => {
            this.$store.dispatch('doNotification', {
              msg: this.$t('message.couldNotUpdateUserBusinessInfo'),
              type: 'error'
            })
          })
      },
      /* Clear data entered on controls*/
      clearData() {
    /*     this.names = ''
        this.businessName = ''
        this.$refs.creationCountry.reset()
        this.$refs.operationCountry.reset()
        this.identification = ''
        this.creationDate = ''
        this.$refs.businessModel.reset()
        this.$refs.hasLocale.reset() */
      },
      /* Return to business type step*/
      goBack(data) {
        this.$emit('onGoBack', data)
      },
      /* On continue button click, validate if data is correctly entered before go to next step*/
      saveInitialData() {
        if (this.$refs.form.validate()) {
            this.$root.$children[0].$emit('hideLoading')
          this.$emit('onContinue')
        }
      },
      calculateMax() {
        var today = new Date()
        var dd = String(today.getDate()).padStart(2, '0')
        var mm = String(today.getMonth() + 1).padStart(2, '0') //January is 0!
        var yyyy = today.getFullYear()
        let formattedDate = yyyy + '-' + mm + '-' + dd
        return formattedDate
      }
    }
  }

</script>
<style>
</style>
