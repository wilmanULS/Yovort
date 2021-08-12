<template>
<v-flex>
	<v-layout row wrap>
		<v-flex xs12>
			<span class="vb-cp-title">{{$t('message.vBusinessCpBusinessInfo')}}</span>
			<span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessCpBusinessInfoDetail')}}</span>
		</v-flex>
	</v-layout>

    <v-flex pt-3>
      <v-layout row wrap style="heigth: 100%!important;">
        <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
          <v-text-field
            ref="name"
            :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
            clearable
            v-model="name"
            hide-details
            :rules="nameRules"
            :color="currentBussinesClub.theme.primary"
            v-bind:label="$t('message.commercialName')"
            required
            @change="updateField('name',name,'identity.name')"
          ></v-text-field>
        </v-flex>
        <v-flex
          xs12
          md6
          :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}"
          v-if="businessControlPanel.businessType!=1"
        >
          <v-text-field
            :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
            ref="businessName"
            v-model="businessName"
            hide-details
            clearable
            required
            :rules="nameRules"
            :color="currentBussinesClub.theme.primary"
            v-bind:label="$t('message.socialReason')"
            @change="updateField('businessName',businessName,'identity.businessName')"
          ></v-text-field>
        </v-flex>
        <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
          <v-autocomplete
            ref="creationCountry"
            :no-data-text="$t('message.nodatafound')"
            maxlength="40"
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
            :class="{'vb-ml': $vuetify.breakpoint.mdAndUp&&businessControlPanel.businessType===1,'vb-mr': $vuetify.breakpoint.mdAndUp&&businessControlPanel.businessType!=1 , 'mb-1':$vuetify.breakpoint.smAndDown }"
            @change="updateField('creationCountry',creationCountry,'identity.creationCountry')"
          >
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
          <v-autocomplete
            ref="operationCountry"
            :no-data-text="$t('message.nodatafound')"
            maxlength="40"
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
            :class="{'vb-mr': $vuetify.breakpoint.mdAndUp&&businessControlPanel.businessType===1,'vb-ml': $vuetify.breakpoint.mdAndUp&&businessControlPanel.businessType!=1 , 'mb-1':$vuetify.breakpoint.smAndDown }"
            @change="updateField('operationCountry',operationCountry,'identity.operationCountry')"
          >
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
              :class="{'vb-ml': $vuetify.breakpoint.mdAndUp&&businessControlPanel.businessType===1,'vb-mr': $vuetify.breakpoint.mdAndUp&&businessControlPanel.businessType!=1 , 'mb-1':$vuetify.breakpoint.smAndDown }"
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
              append-icon="event"
            ></v-text-field>
            <v-date-picker
              :first-day-of-week="1"
              :locale="selectedLocale.locale"
              :color="currentBussinesClub.theme.primary"
              v-model="creationDate"
              scrollable
              min="1850-01-01"
              :max="calculateMax()"
            >
              <v-spacer></v-spacer>
 
            </v-date-picker>
          </v-menu>
        </v-flex>
        <v-flex
          xs12
          md6
          :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}"
          v-if="businessControlPanel.businessType!=1"
        >
          <v-text-field
            ref="identification"
            :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
            clearable
            v-model="identification"
            hide-details
            :color="currentBussinesClub.theme.primary"
            v-bind:label="$t('message.unicIdentificationNumber')"
            required
            :rules="nameRules"
            @change="updateField('identification',identification,'identity.identification')"
          ></v-text-field>
        </v-flex>

			<v-flex text-xs-left text-md-left pl-1 xs12>
				<span style="font-weight:bold!important" class="requeridTitles">* {{$t('message.requiredFields')}}</span>
			</v-flex>
		</v-layout>
	</v-flex>

	<v-layout row wrap pt-3>
		<v-flex xs12>
			<span class="vb-cp-title">{{$t('message.vBusinessCpModelProduct')}}</span>
			<span class="grey--text vb-cp-subtitle text-xs-justify">{{$t('message.vBusinessCpModelProductDetail')}}</span>
		</v-flex>
	</v-layout>
	<v-flex pt-3>
		<v-layout row wrap>
			<v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
				<v-flex :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }">
					<v-layout row wrap>
						<v-flex xs12 md12 class="profile-flex-padding">
							<v-select ref="businessModel" :color="currentBussinesClub.theme.primary" :items="businessModel" :label="$t('message.businessModel')" v-model="businessModelSelected" :loading="businessModelLoading" attach item-text="name" @change="saveBusinessModel(businessModelSelected)"
							 item-value="code" hide-details required :rules="nameRules"></v-select>
						</v-flex>
					</v-layout>
				</v-flex>
			</v-flex>
			<v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
				<v-flex :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }">
					<v-layout row wrap>
						<v-flex xs12 class="profile-flex-padding">
							<v-layout row wrap>
								<v-flex xs12>
									<v-text-field style="margin-bottom:0px!important" :label="$t('message.vBusinessCpTags')" :color="currentBussinesClub.theme.primary" class="mb-1" clearable append-icon="check" @click:append="addNewTag"
									 @keyup.enter="addNewTag" name="name" ref="tagAdd" hide-details v-model="newTag"></v-text-field>
								</v-flex>
							</v-layout>
						</v-flex>
						<v-flex xs12 md12 lg12 pt-2>
							<v-card-text style="padding:0px!important">
								<v-container pa-0>
									<template>
										<v-layout row wrap>
											<div v-for="(tag, index) in tags" :key="index">
												<v-chip style="background: white!important;" outline :color="currentBussinesClub.theme.primary" close @input="removeTag(index)">{{tag}}</v-chip>
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
							<v-combobox v-model="categoriesSelected" :items="businessCategoriesList" item-text="name" item-value="code" :label="$t('message.vBusinessStepAdditionalInfoCategories')" multiple chips
							 :color="currentBussinesClub.theme.primary" :no-data-text="$t('message.nodatafound')">
								<template v-slot:selection="data">
									<v-chip :key="JSON.stringify(data.item.name)" :selected="data.selected" :disabled="data.disabled" style="background: white!important;" outline close :color="currentBussinesClub.theme.primary"
									 @input="removeCategory(data.item.code)">{{ data.item.name}}</v-chip>
								</template>
							</v-combobox>
						</v-flex>
					</v-layout>
				</v-flex>
			</v-flex>

		</v-layout>
	</v-flex>
    <v-layout row wrap>
		<v-flex xs12>
			<span class="vb-cp-title">{{$t('message.vBusinessCpBusinessCiiu')}}</span>
			<span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessCpBusinessCiiuDetail')}}</span>
		</v-flex>
	</v-layout>
 
    <v-layout align-center justify-center row fill-height   v-if="getObjectPath(businessControlPanel.ciiu, 'length',0)==0" >
      <v-flex></v-flex>
      <v-flex
        :xs3="$vuetify.breakpoint.mdAndUp"
        :xs10="$vuetify.breakpoint.smAndDown"
        style="padding-top:20px;"
      >
        <v-card
          style="text-align:center; border-style:solid; border-color:rgba(0,0,0,0.1); border-width:2px;"
          flat
        >
          <h2>{{$t('message.vBusinessCpNoCiiu')}}</h2>
          <div>
            <v-btn
              :color="currentBussinesClub.theme.primary"
              dark
              @click="openCIIUModal()"
            >{{$t('message.vBusinessCpAddCiiu')}}</v-btn>
          </div>
        </v-card>
      </v-flex>
      <v-flex></v-flex>
    </v-layout>

    <v-flex v-if="getObjectPath(businessControlPanel.ciiu, 'length',0)>0">
      <v-layout align-end justify-end row fill-height>
        <v-btn
          dark
          :color="currentBussinesClub.theme.primary"
          @click="openCIIUModal()"
        >{{$t('message.vBusinessCpAddCiiu')}}</v-btn>
      </v-layout>
      <v-data-table :headers="headers" :items="businessControlPanel.ciiu" class="elevation-1">
        <template v-slot:items="props">
          <td>{{ props.item.codes[0] }}</td>
          <td class="text-xs-left">{{ props.item.codes[1] }}</td>
          <td class="text-xs-left">{{ props.item.codes[2] }}</td>
          <td class="text-xs-left">{{ props.item.codes[3] }}</td>
          <td class="text-xs-left">{{ props.item.description }}</td>
          <td style="padding:5px;" class="text-xs-center">
            <v-btn
              icon
              small
              @click="openConfirmDeleteCiiu(props.item._id,props.item.description )"
            >
              <v-icon>mdi-trash-can</v-icon>
            </v-btn>
          </td>
        </template>
      </v-data-table>
    </v-flex>
 	<v-popup-dialog ref="addCiiu" :model="addCiuuDialog" pa-5 :title="$t('message.vBusinessCpAddCiiu')" @onClose="addCiuuDialog = false" @onSave="saveCiiu()">
		<v-form ref="ciiuForm">
			<v-layout row wrap pt-4>
				<v-flex xs12>
					<span :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}" style="color:black">{{$t('message.vBusinessCpCiiuSearch')}}</span>
					<v-autocomplete id="searchCiiu" ref="searchCIIU" :color="currentBussinesClub.theme.primary" hide-no-data hide-selected clearable item-text="description" item-value="description"
					 :placeholder="$t('message.vBusinessCpCiiuSearchOne')" prepend-inner-icon="mdi-database-search" return-object solo :items="itemsCiiu" :loading="isLoadingSearch" @keyup.exact="searchCIIU()" v-model="ciiuDescription">
					</v-autocomplete>
				</v-flex>
				<v-flex xs12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
					<v-select ref="section" :color="currentBussinesClub.theme.primary" :items="section" :label="$t('message.vBusinessCpCiiuSearchSector')" v-model="sectionSelected" :loading="sectionLoading" attach item-text="description"
					 item-value="section" hide-details @change="getDivision()" :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"></v-select>
				</v-flex>
				<v-flex xs12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}" v-if="division && division.length>0">
					<v-select ref="division" :color="currentBussinesClub.theme.primary" :items="division" :label="$t('message.vBusinessCpCiiuSearchDivision')" v-model="divisionSelected" :loading="divisionLoading" attach item-text="description"
					 item-value="division" hide-details @change="getGroup()" :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"></v-select>
				</v-flex>
				<v-flex xs12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}" v-if="group && group.length>0">
					<v-select ref="group" :color="currentBussinesClub.theme.primary" :items="group" :label="$t('message.vBusinessCpCiiuSearchItem')" v-model="groupSelected" :loading="groupLoading" attach item-text="description" item-value="group"
					 hide-details @change="getClase()" :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"></v-select>
				</v-flex>
				<v-flex xs12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}" v-if="clase && clase.length>0">
					<v-select ref="clase" :color="currentBussinesClub.theme.primary" :items="clase" :label="$t('message.vBusinessCpCiiuSearchSubItem')" v-model="claseSelected" :loading="claseLoading" attach item-text="description" item-value="clase"
					 hide-details :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"></v-select>
				</v-flex>
			</v-layout>
		</v-form>
	</v-popup-dialog>
    <v-confirm-dialog
      ref="confirmDelete"
      type="question"
      :heading="$t('message.vBusinessDeleteCiiuHeader')"
      v-bind:message="$t('message.vBusinessDeleteCiiu',{ciiu:  this.ciiuDescriptionSelected})"
      @onConfirm="deleteCiiu()"
    ></v-confirm-dialog>
  </v-flex>
</template>
<script>
import Vue from "vue";
import {
	mapGetters
} from "vuex";
import AppConfig from "Constants/AppConfig";
import confirmDialog from "../../components/ySocialHome/confirmDialog";
import TelephoneInput from "Components/TelephoneInput/vue-tel-input";
import AddCiiu from "./addCiiuInfo";
export default {
  components: {
    "v-telephone-input": TelephoneInput,
    "v-addciiu": AddCiiu
  },
   data() {
    return {
       getObjectPath: Vue.getObjectPath,
      responseBusinessInfo:null,
      /* Data use as fields for v-model in businessInformation section*/
      name: "",
      businessName: "",
      creationCountry: null,
      operationCountry: null,
      loadingCountries: false,
      identification: "",
      creationDate: "",
      menu: false,
      hasLocale: null,
      hasLocaleLoading: false,
      /* Data use as fields for v-model in businessModal section*/
      businessModelSelected: null,
      businessModelLoading: false,
      categoriesSelected: [],
      tags: [],
      newTag: "",
      subCategoriesSelected: [],
      /* Data use as fields for v-model in CIIU section*/
      headers: [
        {
          text: this.$t("message.vBusinessCpCiiuSector"),
          align: "left",
          sortable: false,
          value: "sector"
        },
        { text: this.$t("message.vBusinessCpCiiuDivision"), sortable: false },
        { text: this.$t("message.vBusinessCpCiiuItem"), sortable: false },
        { text: this.$t("message.vBusinessCpCiiuSubItem"), sortable: false },
        { text: this.$t("message.vBusinessCpCiiu"), sortable: false },
        { sortable: false }
      ],
      ciiuInfo: [],
      addCiuuDialog: false,
      selectedCIIUParams: false,
      ciiuDescription: null,
      isLoadingSearch: false,
      itemsCiiu: [],
      ciiuDeleteSelected: "",
      ciiuDescriptionSelected: "",
      /* Validation rules */
      nameRules: [],
      required: "",
      section: [],
      sectionSelected: "",
      sectionLoading: false,
      division: [],
      divisionSelected: "",
      divisionLoading: false,
      group: [],
      groupSelected: "",
      groupLoading: false,
      clase: [],
      claseSelected: "",
      claseLoading: false
      //
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
      "currentUserBusiness",
      "wizardStepOne",
      "businessType",
      "businessModel",
      "confirmationAnswers",
      "businessControlPanel",
      "businessCategoriesList",     
      "businessId",
       
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  watch: {
    ciiuDescription(val) {
      this.bindInfoCIIU(val);
      this.$refs.searchCIIU.reset();
      this.ciiuDescription = null;
    },
    creationDate(val) {
        if (val) {
          this.updateField('creationDate', val, 'identity.creationDate')
        }
      }
  },
  created() {     
    this.required = this.$t("message.required");
    this.nameRules = [v => !!v || this.required];    
    /* Load catalogs lists for this view */
    Vue.nextTick(() => {
     
      this.loadingCountries = true;
      this.$store.dispatch("doGetCountries").finally(() => {
        this.loadingCountries = false;
      });
      /* Get items for business model catalog */
      this.businessModelLoading = true;
      this.$store.dispatch("doGetBusinessModel").finally(() => {
        this.businessModelLoading = false;
      });
      /* Get items for answers confirmation catalog */
      this.hasLocalLoading = true;
      this.$store.dispatch("doGetAnswersConfirmation").finally(() => {
        this.hasLocalLoading = false;
      });
    });
  },
  mounted() {
    Vue.nextTick(() => {
     this.$root.$children[0].$emit('showLoading', 'Cargando negocios del usuario') 
     this.$store
      .dispatch("doGetBusinessControlPanel", {
        businessId: this.businessId,
        menu:'businessInfo'
      })
      .then(res => {          
        this.responseBusinessInfo=res.data.data; 
         let categoryName = ''
       switch (this.responseBusinessInfo.businessModel.model) {
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
        this.$store.dispatch('doGetBusinessCategories', {
         category: categoryName
        }).finally(()=>{
          this.reloadFields();                 
          if(this.$vuetify.breakpoint.mdAndUp){            
              this.$parent.$parent.$parent.$parent.$parent.$el.scrollTop = '400';   
          }
          else{            
              this.$parent.$parent.$parent.$parent.$parent.$el.scrollTop = '0';   
          }            
        })            
      });     
    });
  },
 
  components: {
    "v-confirm-dialog": confirmDialog
  },
  methods: {
      /* Update dinamically the value of a property */
      updateField(field, value, path) {
        /* Validate if field is not empty */
        if (this[field] != '') {
          let data = {
            businessId: this.businessId,
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
          businessId:this.businessId,
          model: this.businessModelSelected,        
        }
        /* Check if there is business model is already inserted on db*/
        let modelId = Vue.getObjectPath(this.responseBusinessInfo, 'businessModel._id', null);
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
    /**
     * Add new tag for business
     */
    addNewTag() {
      /* Check if tag already exists */
      if (!this.newTag || this.newTag.length === 0) {
        return 0;
      }
      /* Check if tag was added previously */
      const tagList = this.tags.filter(
        value => value.toLowerCase() === this.newTag.toLowerCase()
      );
      if (tagList && tagList.length > 0) {
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.errorMessageEmailAlreadyExists"),
          type: "error"
        });
        return -1;
      }
      this.tags.push(this.newTag);
      /* Clear values */
      this.newTag = "";
      return 0;
    },
    /**
     * Remove a business tag
     */
    removeTag(index) {
      this.tags.splice(index, 1);
    },
    reloadFields() {    
      this.name = Vue.getObjectPath(this.responseBusinessInfo, "name", "");      
      this.businessName = Vue.getObjectPath(this.responseBusinessInfo,"businessName","");
      this.creationCountry = Vue.getObjectPath(this.responseBusinessInfo, "creationCountry", null);
      this.creationDate = Vue.getObjectPath(this.responseBusinessInfo,"creationDate","");
      this.operationCountry = Vue.getObjectPath(this.responseBusinessInfo,"operationCountry",null);
      this.identification = Vue.getObjectPath(this.responseBusinessInfo,"identification","");
      this.businessModelSelected = Vue.getObjectPath(this.responseBusinessInfo,"businessModel.model",null);
      if (this.businessModelSelected === -1) {
        this.businessModelSelected = null;
      }      
      this.ciiuInfo=Vue.getObjectPath(this.responseBusinessInfo,"ciiu",[]);
      let arrayCategory = Vue.getObjectPath(this.responseBusinessInfo, 'businessModel.categories', [])    
      this.categoriesSelected = []     
      this.$root.$children[0].$emit('hideLoading')      
      arrayCategory.forEach(element => {         
          this.categoriesSelected.push({
            code: element,
            name: this.businessCategoriesList.filter(c => c.code == element)[0].name
          })
        })   
       
    },
    openCIIUModal() {
      this.selectedCIIUParams = false;
      this.section = [];
      this.division = [];
      this.group = [];
      this.clase = [];
      this.sectionSelected = "";
      this.divisionSelected = "";
      this.groupSelected = "";
      this.claseSelected = "";
      this.$store
        .dispatch("doGetCIIUData", {
          section: "",
          division: "",
          group: "",
          clase: ""
        })
        .then(response => {
          this.addCiuuDialog = true;
          this.section = response;
        })
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.couldNotUpdateUserBusinessInfo"),
            type: "error"
          });
        });
    },
    getCiiu(section, division, group, clase, controlSelected) {
      this.$store
        .dispatch("doGetCIIUData", {
          section: section,
          division: division,
          group: group,
          clase: clase
        })
        .then(response => {
          this[controlSelected] = response;
          this.selectedCIIUParams = true;
          this.ciiuInfo = response;
        })
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.couldNotUpdateUserBusinessInfo"),
            type: "error"
          });
        });
    },
    getDivision() {
      this.group = [];
      this.clase = [];
      this.division = [];
      this.divisionSelected = "";
      this.groupSelected = "";
      this.claseSelected = "";
      this.getCiiu(
        this.sectionSelected,
        this.divisionSelected,
        this.groupSelected,
        this.claseSelected,
        "division"
      );
    },
    getGroup() {
      this.group = [];
      this.groupSelected = "";
      this.clase = [];
      this.claseSelected = "";
      this.getCiiu(
        this.sectionSelected,
        this.divisionSelected,
        this.groupSelected,
        this.claseSelected,
        "group"
      );
    },
    getClase() {
      this.clase = [];
      this.claseSelected = "";
      this.getCiiu(
        this.sectionSelected,
        this.divisionSelected,
        this.groupSelected,
        this.claseSelected,
        "clase"
      );
    },
    searchCIIU() {
      let description = document.getElementById("searchCiiu").value;
      if (description.length > 3) {
        this.isLoadingSearch = true;
        this.$store
          .dispatch("doSearchCIIUByDescription", {
            description: description
          })
          .then(response => {
            this.itemsCiiu = response;
            this.isLoadingSearch = false;
          })
          .catch(() => {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.couldNotUpdateUserBusinessInfo"),
              type: "error"
            });
          });
      }
    },
    getDescriptionCiiu(items) {
      if (items.length == 1) {
        return this.section.filter(sec => sec.section == items[0])[0]
          .description;
      }
      if (items.length == 2) {
        return this.division.filter(div => div.division == items[1])[0]
          .description;
      }
      if (items.length == 3) {
        return this.group.filter(gr => gr.group == items[2])[0].description;
      }
      if (items.length == 4) {
        return this.clase.filter(cl => cl.clase == items[3])[0].description;
      }
      return "";
    },
    saveCiiu() {     
      let data = [];
      data.push(this.sectionSelected);
      data.push(this.divisionSelected);
      data.push(this.groupSelected);
      data.push(this.claseSelected);
      data = data.filter(item => item != "");
      if (data.length > 0) {
        let ciiuDescription = this.getDescriptionCiiu(data);
        /* Check if ciiu was added previously */
        const ciiuList = this.businessControlPanel.ciiu.filter(
          value => value.description.toLowerCase() === ciiuDescription.toLowerCase()
        );
        if (ciiuList && ciiuList.length > 0) {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.vBusinessCpCiiuInsertDuplicated",{ciiu:ciiuDescription}),
            type: "error"
          });
          return -1;
        }
        this.$store
          .dispatch("doUpdateBusinessCiiu", {
            description: ciiuDescription,
            ciiu: data,
            businessId: this.businessId
          })
          .then(response => {
             this.addCiuuDialog = false;
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.vBusinessCpCiiuInsertSuccess"),
              type: "success"
            });
          })
          .catch(() => {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.vBusinessCpCiiuInsertFail"),
              type: "error"
            });
          });
      }else{
          this.$store.dispatch("doNotification", {
              msg: this.$t("message.vBusinessCpCiiuInsertEmpty"),
              type: "error"
            });
      }

    },
    openConfirmDeleteCiiu(id, description) {
      this.ciiuDeleteSelected = id;
      this.ciiuDescriptionSelected = description;
      this.$refs.confirmDelete.openDialog();
    },
    deleteCiiu() {
      this.$store
        .dispatch("doDeleteBusinessCiiu", {
          ciiuId: this.ciiuDeleteSelected,
          businessId: this.businessId
        })
        .then(response => {
          this.$refs.confirmDelete.close();
          this.ciiuDeleteSelected = "";
          this.ciiuDescriptionSelected = "";
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.vBusinessCpCiiuDeleteSuccess"),
            type: "success"
          });
        })
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.vBusinessCpCiiuDeleteFail"),
            type: "error"
          });
        });
    },
    bindInfoCIIU(val) {
      if (val) {
        if (val.section) {
          this.sectionSelected = val.section;
          this.getDivision();
          if (val.division) {
            this.divisionSelected = val.division;
            this.getGroup();
            if (val.group) {
              this.groupSelected = val.group;
              this.getClase();
              if (val.clase) {
                this.claseSelected = val.clase;
              }
            }
          }
        }
        this.$refs.searchCIIU.reset();
        this.ciiuDescription = null;
        this.itemsCiiu = [];
      }
    },
    calculateMax() {
      var today = new Date();
      var dd = String(today.getDate()).padStart(2, "0");
      var mm = String(today.getMonth() + 1).padStart(2, "0"); //January is 0!
      var yyyy = today.getFullYear();
      let formattedDate = yyyy + "-" + mm + "-" + dd;
      return formattedDate;
    },
    /**
     *  For update field of current user business
     */
    updateName(field, value, path) {
      let data = {
        businessId: this.businessId,
        step: "summary",
        field: field,
        path: path
      };
      data[field] = value;
      this.$store
        .dispatch("doUpdateUserBusiness", data)
        .then(() => {
          this[field] = this.wizardStepOne[field];
        })
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.couldNotUpdateUserBusinessInfo"),
            type: "error"
          });
        });
    },
     removeCategory(id) {
         
        let data = {
          businessId: this.businessId,
          modelId:this.responseBusinessInfo.businessModel._id,          
          type:'remove',
          category : id
        }
        /* Update taxonomy data for current business model*/
        this.$store.dispatch('doRegisterBusinessModel', data).
        then(()=>{
           this.categoriesSelected.splice(this.categoriesSelected.findIndex(item => item.code == id), 1)      
        }).
        catch(() => {
          this.$store.dispatch('doNotification', {
            msg: this.$t('message.couldNotUpdateUserBusinessInfo'),
            type: 'error'
          })
        })
         
      },
  }
};
</script>

 