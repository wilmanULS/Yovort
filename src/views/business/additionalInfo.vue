<template>

  <v-flex>
    <v-layout row wrap>
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-flex :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }">
          <v-layout row wrap>
            <v-flex xs12 md12 class="profile-flex-padding">
              <v-text-field style="margin-bottom:0px!important"
                            :label="$t('message.emailContact')"
                            :color="currentBussinesClub.theme.primary"
                            class="mb-1"
                            clearable
                            append-icon="check"
                            @click:append="addNewEmail"
                            @keyup.enter="addNewEmail"
                            v-model="newEmail"
                            name="name"
                            ref="emailAdd"></v-text-field>
            </v-flex>
          </v-layout>
          <v-flex xs12 md12 lg12>
            <v-card-text style="padding:0px!important">
              <v-container pa-0>
                <template>
                                          <v-layout row wrap>
                                            <div v-for="(email, index) in emails" :key="index">
                                              <v-chip
                                                style="background: white!important;"
                                                outline
                                                :color="currentBussinesClub.theme.primary"
                                                close
                                                @input="removeEmail(index)"
                                              >{{email}}</v-chip>
                                            </div>
                                          </v-layout>
                                        </template>
              </v-container>
            </v-card-text>
          </v-flex>
        </v-flex>
      </v-flex>
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-flex :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }">
          <v-layout row wrap>
            <v-flex xs12 class="profile-flex-padding">
              <v-layout row wrap>
                <v-flex xs12>
                  <v-telephone-input ref="number"
                                     :disabledFetchingCountry="true"
                                     v-model="number"
                                     :color="currentBussinesClub.theme.primary"
                                     :placeholder="$t('message.vBusinessStepAdditionalInfoPhones')"
                                     hide-details
                                     :required="true"
                                     :appendIcon="'check'"
                                     @onInput="onInput"
                                     @onEnter="addNewPhone"
                                     style="z-index:2"
                                     :preferredCountries="['ec']"></v-telephone-input>
                </v-flex>
              </v-layout>
            </v-flex>
            <v-flex xs12 md12 lg12 pt-2>
              <v-card-text style="padding:0px!important">
                <v-container pa-0>
                  <template>
                                            <v-layout row wrap>
                                              <div v-for="(phone, index) in phones" :key="index">
                                                <v-chip
                                                  style="background: white!important;"
                                                  outline
                                                  :color="currentBussinesClub.theme.primary"
                                                  close
                                                  @input="removePhoneNumber(index)"
                                                >{{phone}}</v-chip>
                                              </div>
                                            </v-layout>
                                          </template>
                </v-container>
              </v-card-text>
            </v-flex>
          </v-layout>
        </v-flex>
      </v-flex>
    </v-layout>
    <v-layout row wrap>
        <v-flex xs12>
        <v-flex>
          <v-layout row wrap>
            <v-flex xs12>
              <v-combobox v-model="categoriesSelected"
                          :items="businessCategoriesList"
                          item-text="name"
                          item-value="code"
                          :label="$t('message.vBusinessStepAdditionalInfoCategories')"
                          multiple
                          chips
                          :color="currentBussinesClub.theme.primary"
                          :no-data-text="$t('message.nodatafound')">
                <template v-slot:selection="data">
                                          <v-chip
                                            :key="JSON.stringify(data.item.name)"
                                            :selected="data.selected"
                                            :disabled="data.disabled"
                                            style="background: white!important;"
                                            outline
                                            close
                                            :color="currentBussinesClub.theme.primary"
                                            @input="removeCategory(data.item.code)"
                                          >{{ data.item.name}}</v-chip>
                                        </template>
              </v-combobox>
            </v-flex>
          </v-layout>
        </v-flex>
      </v-flex> 
    </v-layout>

    <v-layout row wrap>
        <v-flex xs12  style="margin-bottom:0px!important">
             <v-switch
                    :color="currentBussinesClub.theme.primary"
                    :label="$t('message.vBusinessStepAdditionalInfoIFactory')"
                    style="margin-bottom:0px!important"
                    v-model="factory"
                  ></v-switch>
        </v-flex>
        <v-flex xs12  style="margin-bottom:0px!important">
               <v-switch
                    :color="currentBussinesClub.theme.primary"
                       :label="$t('message.vBusinessStepAdditionalInfoIImporter')"
                    style="margin-bottom:0px!important"
                    v-model="importer"
                  ></v-switch>
        </v-flex>
        <v-flex xs12  style="margin-bottom:0px!important">
              <v-switch
                    :color="currentBussinesClub.theme.primary"
                      :label="$t('message.vBusinessStepAdditionalInfoIExporter')"
                    style="margin-bottom:0px!important"
                    v-model="exporter"
                  ></v-switch>
        </v-flex>
    </v-layout>

    <v-flex pt-3>
      <v-btn :color="currentBussinesClub.theme.primary" dark @click="saveAdditionalInfo()">{{$t('message.vBusinessStepBusinessContinue')}}</v-btn>
      <v-btn flat
             @click="goBack(3)"
             :color="currentBussinesClub.theme.secondary">{{$t('message.vBusinessStepBusinessBack')}}</v-btn>
    </v-flex>
  </v-flex>

</template>
<script>

  import Vue from 'vue'
  import { mapGetters } from 'vuex'
  import { EventBus } from '../../eventBus.js'
  import TelephoneInput from 'Components/TelephoneInput/vue-tel-input'
  export default {
    components: {
      'v-telephone-input': TelephoneInput
    },
    props: {
      currentBusinessId: String,
      stepData: Object,
      hasLocale: Number
    },
    data() {
      return {
        /* Vars for validations*/
        phoneValid: '',
        newEmail: '',
        prefix: '',
        number: '',
        subCategoriesLoading: false,
        readOnlySubCategories: true,
        /* Data use as fields for v-model */
        emails: [],
        phones: [],
        categoriesSelected: [],
        subCategoriesSelected: [],
        factory:false,
        importer:false,
        exporter:false,
      }
    },
    watch: {
      categoriesSelected(val) {
      /*   this.subCategoriesLoading = true
        this.readOnlySubCategories = true
        this.getSubCategory(val)
        this.subCategoriesLoading = false
        this.readOnlySubCategories = false */
        //console.log(val)
      }  
    },
    computed: {
      ...mapGetters([
        'userProfile',
        'currentBussinesClub',
        'businessCategoriesList',
        'businessSubCategoriesList',
        'selectedLocale'
      ])
    },
    created() {
      EventBus.$on('clearWizard', () => {
        this.clearData()
      })
      EventBus.$on('removeCategories', (data) => {
        if(data.exists){
           data.forEach(element=>{           
          this.categoriesSelected=data
        })
        }
        else{        
        /*   console.log("lo que tengo",this.categoriesSelected)  
          console.log("lo que llega",data.categories)   */
          this.categoriesSelected.forEach(function(item, index, object) {
          let category = data.categories.filter(value => value == item.code)
         // console.log(category)
          if(!category||category.length==0) {
              object.splice(index, 1);          
          }          
            
          });          
        }
      })
      Vue.nextTick(() => {})
      // let categoryName = ''
      // switch (this.stepData.businessType) {
      //   case 1:
      //     categoryName = 'products'
      //     break
      //   case 2:
      //     categoryName = 'services'
      //     break
      //   default:
      //     categoryName = 'all'
      //     break
      // }
      // this.$store.dispatch('doGetBusinessCategories', {
      //   category: categoryName
      // })
    },
    mounted() {
      this.reloadFields()
    },
    methods: {
      reloadFields() {
      if (this.hasLocale == 1) {          
          this.emails = Vue.getObjectPath(this.stepData, 'addressContact.0.emails', []) 
          this.phones = Vue.getObjectPath(this.stepData, 'addressContact.0.phones', [])
        } else {
          this.emails = Vue.getObjectPath(this.stepData, 'businessContact.emails', [])
          this.phones = Vue.getObjectPath(this.stepData, 'businessContact.phones', [])
        }
        this.factory =Vue.getObjectPath(this.stepData, 'businessActivity.factory', false);
        this.importer=Vue.getObjectPath(this.stepData, 'businessActivity.importer', false);
        this.exporter=Vue.getObjectPath(this.stepData, 'businessActivity.exporter', false);

        let arrayCategory = Vue.getObjectPath(this.stepData, 'businessModel.categories', [])
        this.categoriesSelected = []      
        arrayCategory.forEach(element => {          
          this.categoriesSelected.push({
            code: element,
            name: this.businessCategoriesList.filter(c => c.code == element)[0].name
          })
        })
      },
      clearData() {
        this.newEmail = ''
        this.number = ''
        this.emails = []
        this.phones = []
        this.categories = []
        this.subCategories = []
      },
      goBack(data) {
        this.$emit('onGoBack', data)
      },
      saveAdditionalInfo() {    
        /*Validate empty fields */
        if (
          this.emails.length == 0 ||
          this.phones.length == 0 ||
          this.categoriesSelected.length == 0 
        ) {
          this.$store.dispatch('doNotification', {
            msg: this.$t('message.alertMessage'),
            type: 'error'
          })
          return
        }   
         this.$root.$children[0].$emit('showLoading',  this.$t('message.vBusinessSavingData'))
        /* Prepare data to insert into business model*/
        let modelId = Vue.getObjectPath(this.stepData, 'businessModel._id', null);
        if(!modelId){
          /* This should never happens */
          return;
        }
        let categories = []
        this.categoriesSelected.forEach(element=>{
          categories.push(element.code);
        })
        let data = {
          businessId: this.currentBusinessId,
          modelId:modelId,          
          type:'category',
          category : categories
        }
        /* Update taxonomy data for current business model*/
        this.$store.dispatch('doRegisterBusinessModel', data).catch(() => {
          this.$store.dispatch('doNotification', {
            msg: this.$t('message.couldNotUpdateUserBusinessInfo'),
            type: 'error'
          })
        })

        /* Update business activity */
        let activityData={
          factory:this.factory,
          importer:this.importer,
          exporter:this.exporter
        }
           let businessActivityData = {
            businessId: this.currentBusinessId,
            field: 'businessActivity',
            businessActivity: activityData,
            path: 'businessActivity'
          }
        this.$store.dispatch('doUpdateUserBusiness', businessActivityData).catch(() => {
          this.$store.dispatch('doNotification', {
            msg: this.$t('message.couldNotUpdateUserBusinessInfo'),
            type: 'error'
          })
        })
        
        /* If business has locale this info for email and phones are going to be related to main
                                 address business inserted on Address step */
        
        if (this.hasLocale == 1) {
          let addressData = {
            _id: Vue.getObjectPath(this.stepData, 'addressContact.0._id', null),
            emails: this.emails,
            phones: this.phones,
            type:'contact'
          }    
          
          this.$store
            .dispatch('doAddBusinessAddress', {
              businessId: this.currentBusinessId,
              address: addressData
            })
            .then(res => {
                  this.$root.$children[0].$emit('hideLoading')
              this.$emit('onContinue')
            })
            .catch(err => {
              this.$store.dispatch('doNotification', {
                msg: this.$t('message.couldNotAddBusinessAddress'),
                type: 'error'
              })
            })
        } else {
          /* Info for email and phones are going to related to business */
          let businessContact = {
            emails: this.emails,
            phones: this.phones
          }
          let data = {
            businessId: this.currentBusinessId,
            field: 'businessContact',
            businessContact: businessContact,
            path: 'businessContact'
          }
          this.$store
            .dispatch('doUpdateUserBusiness', data)
            .then(() => {
                  this.$root.$children[0].$emit('hideLoading')
              this.$emit('onContinue')
              
            })
            .catch(() => {
              this.$store.dispatch('doNotification', {
                msg: this.$t('message.couldNotUpdateUserBusinessInfo'),
                type: 'error'
              })
            })
        }
      },
      /* Add new contact email */
      addNewEmail() {
        /* Check if the email exists as data on control */
        if (!this.newEmail || this.newEmail.length === 0) {
          return 0
        }
        /* Check if the email is valid */
        if (
          !/^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
            this.newEmail
          )
        ) {
          this.$store.dispatch('doNotification', {
            msg: this.$t('message.errorMessageWrongEmail'),
            type: 'error'
          })
          return -1
        }
        /* Check if the email was added previously */
        const emailList = this.emails.filter(value => value.toLowerCase() === this.newEmail.toLowerCase())
        if (emailList && emailList.length > 0) {
          this.$store.dispatch('doNotification', {
            msg: this.$t('message.errorMessageEmailAlreadyExists'),
            type: 'error'
          })
          return -1
        }
        this.emails.push(this.newEmail)
        /* Clear values */
        this.newEmail = ''
        return 0
      },
      /* Add new phone number */
      addNewPhone() {
        /* Check if the phone number exists */
        if (!this.number || this.number.length === 0) {
          return 0
        }
        /* Check if the phone number is valid */
        if (!this.phoneValid) {
          this.$store.dispatch('doNotification', {
            msg: this.$t('message.errorMessageWrongPhone'),
            type: 'error'
          })
          return -1
        }
        /* Check if the phone was added previously */
        const phones = this.phones.filter(value => value.number === this.number)
        if (phones && phones.length > 0) {
          this.$store.dispatch('doNotification', {
            msg: this.$t('message.errorMessagePhoneInUse'),
            type: 'error'
          })
          return -1
        }
        /* Add the phone to the list */
        this.phones.push(this.number)
        /* Clear values */
        this.number = ''
        this.phoneValid = false
        return 0
      },
      /* Event triggered on phone number change */
      onInput(payload) {
        this.phoneValid = payload.isValid
        this.number = payload.number
      },
      getTaxonomy() {
        let objectTaxonomy = []
        let category = -1
        this.categoriesSelected.forEach(c => {
          let subCategories = []
          if (c) {
            category = c.code
            //let result = this.subCategoriesSelected.filter(subcategory => subcategory.parent == c.code)
            /* result.forEach(item => {
              subCategories.push(item.code)
            }) */
            objectTaxonomy.push({
              category: category
              //subCategories: subCategories
            })
          }
        })
        return objectTaxonomy
      },
      getSubCategory(categories, cb) {
        let categoryName = ''
        let model = Vue.getObjectPath(this.stepData, 'businessModel.model', -1)
        /* model should always return a value because this was entered on second step*/
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
        let result = []
        this.$store
          .dispatch('doGetBusinessSubCategories', {
            categories: categories,
            search: {
              lang: this.selectedLocale.code,
              category: categoryName,
              level: 2
            }
          })
          .finally(() => {
            if (cb) {
              cb()
            }

            this.subCategoriesLoading = false
            this.readOnlySubCategories = false
          })
      },

      /* Remove a selected email address */
      removeEmail(index) {
        this.emails.splice(index, 1)
      },
      /* Remove a selected phone number */
      removePhoneNumber(index) {
        this.phones.splice(index, 1)
      },
      /* Remove a category selected with all its subcategories related */
      removeCategory(id) {
        this.categoriesSelected.splice(this.categoriesSelected.findIndex(item => item.code == id), 1)
        //this.subCategoriesSelected = this.subCategoriesSelected.filter(item => item.parent != id)
        //this.getSubCategory(this.categoriesSelected)
      },
       /* Remove a subCategory selected*/
      removeSubCategory(id) {
        this.subCategoriesSelected.splice(this.subCategoriesSelected.findIndex(item => item.code == id), 1)
      }
    }
  }

</script>

<style scoped>

  .mrAddress {
    margin-right: 1rem !important;
  }

  .mlAddress {
    margin-left: 1rem !important;
  }
  .v-text-field__details {
    display: none;
  }
  .dropdown {
    padding-left: 0px !important;
  }
  .v-select__selections {
    display: none;
  }

</style>

