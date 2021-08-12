<template>
  <v-form data-vv-scope="step1" pa-4>
   <v-layout row wrap >
      <v-flex xs12 md6 class="padding-step-wizard">
        <v-text-field
         :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          clearable
          v-model="name"
          hide-details
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.commercialName')"
          @change="updateName('name',name,'identity.name')"
          required
        ></v-text-field>
      </v-flex>
      <v-flex  xs12 md6 class="padding-step-wizard">
        <v-text-field
          v-model="businessName"
          hide-details
          clearable
          required
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.socialReason')"
          @change="updateName('businessName',businessName,'identity.businessName')"
        ></v-text-field>
      </v-flex>
   </v-layout>
   <v-layout row wrap >
      <v-flex xs12 md6 class="padding-step-wizard">
        <v-autocomplete
          ref="creationCountry"
          :no-data-text="$t('message.nodatafound')"
          :items="countries"
          item-text="name"
          item-value="_id"
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.selectCountryCreation')"
          hide-details
          v-model="creationCountry"
          attach
          :loading="loadingCountries"
          @change="updateName('creationCountry',creationCountry,'identity.creationCountry')"
          :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
        >
          <template slot="selection" slot-scope="data">
            <v-flex class="ml-1">
              <flag :iso="data.item.ISO.alpha2"/>
              {{ data.item.name }}
            </v-flex>
          </template>
          <template slot="item" slot-scope="data">
            <v-list-tile-content>
              <v-list-tile-title>
                <flag :iso="data.item.ISO.alpha2"/>
                {{data.item.name}}
              </v-list-tile-title>
            </v-list-tile-content>
          </template>
        </v-autocomplete>
      </v-flex>
      <v-flex xs12 md6 class="padding-step-wizard">
        <v-autocomplete
          ref="operationCountry"
          :no-data-text="$t('message.nodatafound')"
          :items="countries"
          item-text="name"
          item-value="_id"
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.selectCountryOperation')"
          hide-details
          v-model="operationCountry"
          attach
          :loading="loadingCountries"
          @change="updateName('operationCountry',operationCountry,'identity.operationCountry')"
        >
          <template slot="selection" slot-scope="data">
            <v-flex class="ml-1">
              <flag :iso="data.item.ISO.alpha2"/>
              {{ data.item.name }}
            </v-flex>
          </template>
          <template slot="item" slot-scope="data">
            <v-list-tile-content>
              <v-list-tile-title>
                <flag :iso="data.item.ISO.alpha2"/>
                {{data.item.name}}
              </v-list-tile-title>
            </v-list-tile-content>
          </template>
        </v-autocomplete>
      </v-flex>
   </v-layout>
      <v-layout row wrap >
      <v-flex xs12 md4  class="padding-step-wizard">
        <v-select
          :color="currentBussinesClub.theme.primary"
          :items="businessType"
          :label="$t('message.selectTypeBusiness')"
          v-model="selectTypeBusiness"
          :loading="businessTypeLoading"
          attach
          item-text="text"
          item-value="value"
           hide-details
          @change="updateName('description',selectTypeBusiness,'identity.type.description')"
        ></v-select>
      </v-flex>
      <v-flex xs12 md4  class="padding-step-wizard">
        <v-text-field
          clearable
          v-model="identification"
          :readonly="showRUC"
          :disabled="showRUC"
           hide-details
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.unicIdentificationNumber')"
          mask="############"
          @change="updateName('identification',identification,'identity.identification')"
        ></v-text-field>
      </v-flex>
      <v-flex xs12 md4  class="padding-step-wizard">
        <v-select
          attach
           hide-details
          :readonly="showBusinessSize"
          :disabled="showBusinessSize"
          :color="currentBussinesClub.theme.primary"
          :items="businessSize"
          :label="$t('message.size')"
          v-model="selectSizeBusiness"
          :loading="businessSizeLoading"
          item-text="text"
          item-value="value"
          @change="updateName('size',selectSizeBusiness,'identity.type.size')"
        ></v-select>
      </v-flex>
      </v-layout>
         <v-layout row wrap >
      <v-flex  xs12 md6 class="padding-step-wizard">
        <v-menu

          ref="menu"
          v-model="menu"
          :close-on-content-click="false"
          :nudge-right="40"
          lazy
          transition="scale-transition"
          offset-y
          full-width
          min-width="290px"
        >
          <v-text-field
            class="mr05"
             readonly=""
            :color="currentBussinesClub.theme.primary"
            slot="activator"
            v-model="creationDate"
            v-bind:label="$t('message.creationDate')"
            hide-details
          ></v-text-field>
          <v-date-picker
            :first-day-of-week="1"
            :locale="selectedLocale.locale"
            :color="currentBussinesClub.theme.primary"
            v-model="creationDate"
            scrollable
            :max="calculateMax()"
          >
            <v-spacer></v-spacer>
            <v-btn
              flat
              :color="currentBussinesClub.theme.secondary"
              @click="menu = false;cancelSetDate()"
            >{{$t('message.cancel')}}</v-btn>
            <v-btn
              flat
              :color="currentBussinesClub.theme.primary"
              @click="$refs.menu.save(creationDate); setAuxCreationDate()"
            >{{$t('message.ok')}}</v-btn>
          </v-date-picker>
        </v-menu>
      </v-flex>
      <v-flex xs12 md6 class="padding-step-wizard">
         <v-select
          :color="currentBussinesClub.theme.primary"
          :items="ciiuList"
          :label="$t('message.selectTypeBusiness')"
          v-model="ciiu"
          :loading="ciiuLoading"
          attach
          item-text="text"
          item-value="value"
          hide-details
          @change="updateName('ciiu',ciiu,'identity.ciiu')"
        ></v-select>
      </v-flex>
         </v-layout>

           <v-confirm-dialog v-bind:titleToolbar="$t('message.delete')" ref="confirmDelete" type="question"
             :heading="$t('message.deleteAdministratorTip')"
             v-bind:message="$t('message.deleteAdministratorConfirmation',{admin:  this.selectedAdminNames})"
             @onConfirm="deleteBusinessAdministrator()">
        </v-confirm-dialog>
  </v-form>
</template>
<script>
import Vue from "vue";

import { mapGetters } from "vuex";
import AppConfig from "Constants/AppConfig" ;
import confirmDialog from "../../components/ySocialHome/confirmDialog";
export default {

  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "currentUserBusiness",
      "wizardStepOne",
      "businessType",
      "businessSize",
      "ciiuList"
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
  watch: {
    selectTypeBusiness(val) {
      /* Items set by default until real items definition arrives. You must change this according database */
      switch (val) {
        case "5cc09d9dd072cc4a301e3190": //Persona juridica
          this.showBusinessSize = false;
          this.showRUC = false;
          break;
        case "5cc09d9dd072cc4a301e3198": //Nuevo emprendimiento
          this.showBusinessSize = true;
          this.showRUC = true;
          this.selectSizeBusiness = "";
          break;
        case "5cc09d9dd072cc4a301e3199": //Persona natural
          this.showBusinessSize = true;
          this.showRUC = false;
          this.selectSizeBusiness = "";
          break;
        default:
          this.showBusinessSize = true;
          this.showRUC = true;
          break;
      }
    },
      creationDate(val){
        if(this.creationDate)
        {
          if(val!=this.auxCreationDate)
          {
             this.updateName('creationDate',this.creationDate,'identity.creationDate');
          }
        }
      },
    /**
     * When user is typing on administrator autocomplete then view is watching input to search.
     */
    search(val) {
      /**
       * If searchResult is not null then it means user selects an item, so it tries to save selected user as admin of business
       * otherwise just watch if input continues entering.
       */
      if (this.searchResult) {
        this.$store
          .dispatch("doAddBusinessAdministrator",{businessId:this.currentUserBusiness, adminInfo:{userId:this.searchResult}})
          .then(()=>{
            this.$store.dispatch("doNotification", {
            msg: this.$t('message.administratorAddSuccess'),
            type: "success"
          });
          })
          .catch()
          .finally(() => {
            this.searchResult = null;
            this.users = [];
             this.getAdministrators();
            return;
          });
      }
      /**
       * When user is entering input data and its length is greater that 3 then start to search on database.
       */
      if (val) {
        if (val.length >= 3) {
          this.$store
            .dispatch("doSearchUsersByName", { page: 1, size: 10, search: val })
            .then(res => {
              let usersResult = res.data.data.page;
              /**
               * If there is data on response then add this data to items which will show on autocomplete
               * displaying name, avatar and id of users.
               */
              if (usersResult && usersResult.length > 0) {
                usersResult.forEach(user => {
                  this.users.push({
                    name:
                      user.personal_info.names +
                      " " +
                      user.personal_info.surnames,
                      avatar:`${AppConfig.FMS_URL}/image/fetch/${user.personal_info.picture}?tx=resize(w:64,h:64)&username=${user.personal_info.names + ' ' + user.personal_info.names}&color=${user.personal_info.color}&size=64`,

                    id: user.userId
                  });
                });
              }
            })
            .catch()
            .finally(() => (this.isLoadingData = false));
        } else {
          this.searchResult = null;
          this.users = [];
        }
      }
    }
  },
  created() {

    /* This is used when page is refreshed, so it prevents currentBusiness state lost. */
    /**
     * Get current business Id from store
     */
    this.$root.$children[0].$emit(
				"showLoading",this.$t('message.loadingUserBusinessInfo')
			);
    this.$store.commit("mGetCurrentUserBusiness");
    /* Load catalog lists of step one */
    Vue.nextTick(() => {
      this.loadingCountries=true;
      this.$store.dispatch("doGetCountries").finally(() => {
           this.loadingCountries=false;
      });
      /* Get items for business type catalog */
      this.businessTypeLoading=true;
      this.$store.dispatch("doGetBusinessType",{type:"business"})
      .finally(() => {
        this.businessTypeLoading=false;
      });
      /* Get items for business size catalog */
      this.businessSizeLoading=true;
      this.$store.dispatch("doGetBusinessSize",{type:"size"})
      .finally(() => {
        this.businessSizeLoading=false;
      });
      /* Get items for ciiu catalog */
      this.ciiuLoading=true;
      this.$store.dispatch("doGetCiiuList",{type:"ciiu"})
      .finally(() => {
        this.ciiuLoading=false;
      });
      });


    /**
     * Get info of wizard step one by current business id
     */
    this.$store
      .dispatch("doGetInfoBusinessWizardStep", {
        businessId: this.currentUserBusiness,
        step: "summary"
      })
      .then(() => {
        this.chargeInfoStepOne();
      })
      .catch()
      .finally(() => {
        (this.isLoading = false), (this.viewReady = true);
        	this.$root.$children[0].$emit("hideLoading");
      });
  },
  data() {
    return {
      ciiuLoading:false,
      businessSizeLoading:false,
      loadingCountries:false,
      businessTypeLoading:false,
      selectedAdmin:'-1',
      selectedAdminNames:'',
      headers: [
        {
					text: "",
					align: "left",
					value: "picture",
					sortable: false,
					width: "100"
				},
        {
					text: this.$t('message.admins'),
					align: "left",
          value: "names",
          sortable: false,
        },
        {
					text: "",
					align: "left",
					sortable: false,
        }
      ],
      adminUsers: [

      ],
      descriptionLimit: 60,
      entries: [],
      isLoadingData: false,
      model: null,
      search: null,
      searchResult: null,
      users: [],
      isLoading: false,
      showRUC: true,
      showBusinessSize: true,

      menu: false,
      adminsList: [],
      adminName: "",
      viewReady: false,
      /* Data use as fields for v-model in step one*/
      name: "",
      businessName: "",
      creationCountry: "",
      operationCountry: "",
      taxIdentity: "",
      selectTypeBusiness: "", //review this later
      selectSizeBusiness: "", //review this later
      identification: "",
      creationDate: "",
      ciiu: "",
      searchUserInput: "",
      administrators: [],
      /*Var auxiliar */
      auxCreationDate:""
    };
  },
  components: {
    "v-confirm-dialog": confirmDialog
  },
  methods: {
     openConfirmationDialog(id,name){
     this.selectedAdmin=id;
     this.selectedAdminNames=name;
     this.$refs.confirmDelete.openDialog();
    },
    calculateMax() {
      var today = new Date();
      var dd = String(today.getDate()).padStart(2, "0");
      var mm = String(today.getMonth() + 1).padStart(2, "0"); //January is 0!
      var yyyy = today.getFullYear();

      let formattedDate = yyyy + "-" + mm + "-" + dd;

      return formattedDate;
    },
    addAdmin() {
      let admin = {
        fullName: this.adminName
      };
      this.adminsList.push(admin);
      this.adminName = "";
    },
    deleteAdmin(index) {
      this.adminsList.splice(index, 1);
    },
    /**
     *  Set all fields info related to wizard step one
     */
    chargeInfoStepOne() {
      this.name = Vue.getObjectPath(this.wizardStepOne, 'name', "");
      this.businessName = Vue.getObjectPath(this.wizardStepOne, 'businessName', "");
      this.creationCountry = Vue.getObjectPath(this.wizardStepOne, 'creationCountry', "");
      this.operationCountry = Vue.getObjectPath(this.wizardStepOne, 'operationCountry', "");
      this.creationDate = Vue.getObjectPath(this.wizardStepOne, 'creationDate', "");
      this.auxCreationDate=Vue.getObjectPath(this.wizardStepOne, 'creationDate', ""); //review this later
      this.selectTypeBusiness=Vue.getObjectPath(this.wizardStepOne, 'type.description', "");
      this.selectSizeBusiness=Vue.getObjectPath(this.wizardStepOne, 'type.size', "");
      this.identification= Vue.getObjectPath(this.wizardStepOne, 'identification', "");
      this.ciiu = Vue.getObjectPath(this.wizardStepOne, 'ciiu', "");
      this.getAdministrators();
    },
    cancelSetDate()
    {
      if(this.creationDate!=this.auxCreationDate)
        this.updateName('creationDate',this.auxCreationDate,'identity.creationDate');
    },
    setAuxCreationDate()
    {
      this.auxCreationDate=this.creationDate;
    },

    getAdministrators(){
      this.$store.dispatch("doGetBusinessAdministrators",{ businessId: this.currentUserBusiness}).then(admins=>
      {
        let administratorsID = admins.data.data;
        let usersID=[];
        administratorsID.forEach(admin => {
          usersID.push(admin.userId)
        });
        this.$store.dispatch("doBindUserProfile",{users:usersID}).then(profile=>{
          let profileResult=profile.data.data;
          this.adminUsers=[];

          for (let i in profileResult) {
                  this.adminUsers.push(
                    {
                      names: profileResult[i].firstName + " "+profileResult[i].lastName,
                      picture :`${AppConfig.FMS_URL}/image/fetch/${profileResult[i].photoURL}?tx=resize(w:64,h:64)&username=${ profileResult[i].firstName  + ' ' +  profileResult[i].lastName }&color=${ profileResult[i].color }&size=64`,
                      id:profileResult[i].userId
                    })
          }

        }).catch().finally(()=>{
          this.viewReady = true;
        })
      }
      ).catch()
    },
    deleteBusinessAdministrator()
    {
      this.$store.dispatch("doDeleteBusinessAdministrator",{ businessId: this.currentUserBusiness, userId:this.selectedAdmin})
      .then(()=>{
           this.$store.dispatch("doNotification", {
            msg: this.$t('message.administratorDeletedSuccess'),
            type: "success"
          });
      })
      .catch(()=>{
        this.$store.dispatch("doNotification", {
            msg: this.$t('message.couldNotDeleteUserBusinessAdmin'),
            type: "error"
          });
      })
      .finally(()=>{
         this.getAdministrators();
         this.selectedAdmin='-1';
         this.selectedAdminNames='';
         this.$refs.confirmDelete.close();
      })
    },
    /**
     *  For update field of current user business
     */
    updateName(field, value, path) {
      let data = {
        businessId: this.currentUserBusiness,
        step: "summary",
        field: field,
        path: path
      };
      data[field] = value;
      this.$store
        .dispatch("doUpdateUserBusiness", data)
        .then(()=>{
            this[field]=this.wizardStepOne[field];
        })
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t('message.couldNotUpdateUserBusinessInfo'),
            type: "error"
          });
        }
        )
    },
  }
};
</script>
<style>
.customBadge {
	width: 24px;
	height: 24px;
	padding-top: 1px;
	position: absolute;
	top: -5px;
	right: -5px;
  border-radius: 50%;
	font-size: 12px;
	font-weight: 400;
}
.padding-step-wizard{
 padding-bottom: 8px!important;
 padding-top: 0px!important;
}
</style>
