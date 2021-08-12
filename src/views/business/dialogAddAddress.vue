<template>
  <v-flex xs12 md12 class="profile-flex-padding">
    <v-dialog
      v-model="addressDialog"
      :fullscreen="$vuetify.breakpoint.xsOnly"
      max-width="700"
      persistent
    >
      <v-card>
        <v-toolbar class="linear-gradient" dense card dark>
          <v-btn absolute right icon dark @click="closeAddressDialog()">
            <v-icon>close</v-icon>
          </v-btn>
          <v-toolbar-title>{{addressIndex<0 ?$t('message.addOfficeAddress'):$t('message.editOfficeAddress')}}</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items></v-toolbar-items>
        </v-toolbar>
        <v-container pt-0 >
          <v-form ref="address" onSubmit="return false;" lazy-validation>
            <v-flex xs12 md12 style="margin-bottom: -25px;">
              <v-checkbox
              :color="currentBussinesClub.theme.primary"
              v-model="radioGroup"
              :label="$t('message.matrix')"
            ></v-checkbox>
            </v-flex>
            <v-flex xs12 md12 class="profile-flex-padding">
              <v-text-field
                :color="currentBussinesClub.theme.primary"
                v-bind:label="$t('message.address')"
                v-model="address.address.fullAddress"
              ></v-text-field>
            </v-flex>
            <v-layout row wrap>
              <v-flex xs12 md12 pl-1 pr-1>

              <v-yovirt-map ref="map" style="height: 500px;" :keyMap="locationMap"></v-yovirt-map>
              </v-flex>
            </v-layout>

            <v-layout row wrap>
              <v-flex xs12 md12 class="profile-flex-padding">
                <v-text-field
                  v-model="emailContact"
                  :label="$t('message.emailContact')"
                  :color="currentBussinesClub.theme.primary"
                  append-icon="check"
                  @click:append="addEmail()"
                  @keyup.enter="addEmail()"
                ></v-text-field>
              </v-flex>
            </v-layout>
            <v-flex xs12 md12 lg12 class="pb-4">
              <v-card-text class="borderCards">
                <v-container>
                  <v-layout
                    align-center
                    justify-center
                    column
                    fill-height
                    v-if="address.phones.length<1 "
                  >
                    <span class="noContent">{{$t('message.emptyemails')}}</span>
                  </v-layout>
                  <template>
                    <v-layout row wrap>
                      <div v-for="(email, index) in address.emails" :key="index">
                        <v-chip
                          style="background: white!important;"
                          outline
                          :color="currentBussinesClub.theme.primary"
                          :ref="'chipEmail'+index"
                          close
                          @input="deleteEmail(index)"
                        >{{email}}</v-chip>
                      </div>
                    </v-layout>
                  </template>
                </v-container>
              </v-card-text>
            </v-flex>
            <v-layout row wrap>

            <v-flex xs12 class="profile-flex-padding">
                    <v-layout row wrap>
              <v-flex xs12>
                            <v-telephone-input :class="{'ml05': $vuetify.breakpoint.mdAndUp}" :phoneRules="phoneRules" ref="number" :disabledFetchingCountry="true" v-model="number" :color="currentBussinesClub.theme.primary"
                                :placeholder="$t('message.phoneNumber')" hide-details :required="true" :appendIcon="'check'" @onEnter="addPhone" :preferredCountries="['ec']">
                            </v-telephone-input>
                        </v-flex>
                        </v-layout>
                        </v-flex>
              <v-flex xs12 md12 lg12 pt-4 class="pb-4">
                <v-card-text class="borderCards">
                  <v-container>
                    <v-layout
                      align-center
                      justify-center
                      column
                      fill-height
                      v-if="address.phones.length<1 "
                    >
                      <span class="noContent">{{$t('message.emptyphones')}}</span>
                    </v-layout>
                    <template>
                      <v-layout row wrap>
                        <div v-for="(phones, index) in address.phones" :key="index">
                          <v-chip
                            style="background: white!important; display: block;"
                            outline
                            :color="currentBussinesClub.theme.primary"
                            :ref="'chipPhones'+index"
                            close
                            @input="deletePhones(index)"
                          >{{phones.prefix}}{{phones.number}}</v-chip>
                        </div>
                      </v-layout>
                    </template>
                  </v-container>
                </v-card-text>
              </v-flex>
            </v-layout>
            <vue-schedule-office></vue-schedule-office>

            <v-flex xs12 sm12 md12 lg12 class="pa-2 text-xs-left">
              <v-autocomplete
                :color="currentBussinesClub.theme.primary"
                :items="servicesList"
                v-model="servicesSelected"
                clearable
                :label="$t('message.services')"
                :no-data-text="$t('message.nodatafound')"
                multiple
                chips
              >
                <template v-slot:selection="data">
                  <v-chip
                    style="background: white!important;"
                    outline
                    :color="currentBussinesClub.theme.primary"
                    :selected="data.selected"
                    close
                    @input="removeService(data.item)"
                  >
                    <strong>{{ data.item }}</strong>
                  </v-chip>
                </template>
              </v-autocomplete>
            </v-flex>
            <v-flex xs12 sm12 md12 lg12 class="pa-2 text-xs-left">
              <h4 style="color:gray">{{$t('message.businessPhotos')}}</h4>
            </v-flex>
            <v-flex xs12 md12 lg12 class="pb-4" style="text-align: center; justify-content: center">
              <vue-upload-multiple-image
                @upload-success="uploadImageSuccess"
                @before-remove="beforeRemove"
                @edit-image="editImage"
                @data-change="dataChange"
                :data-images="images"
                :dragText="$t('message.selectPhotosBusiness')"
                browseText
                primaryText
                popupText
                markIsPrimaryText
              ></vue-upload-multiple-image>
            </v-flex>
     <!--        <v-flex xs12 md12 lg12 class="pb-1">
              <v-switch
                :color="currentBussinesClub.theme.primary"
                v-model="office.questions.question1"
                :label="$t('message.questionServices')"
              ></v-switch>

              <v-switch
                :color="currentBussinesClub.theme.primary"
                v-model="office.questions.question2"
                :label="$t('message.questionStockMarket')"
              ></v-switch>

              <v-switch
                :color="currentBussinesClub.theme.primary"
                v-model="office.questions.question3"
                :label="$t('message.questionPaymentRemittances')"
              ></v-switch>

              <v-switch
                :color="currentBussinesClub.theme.primary"
                v-model="office.questions.question4"
                :label="$t('message.questionSellsOnCredit')"
              ></v-switch>

              <v-switch
                :color="currentBussinesClub.theme.primary"
                v-model="office.questions.question5"
                :label="$t('message.questionSociety')"
              ></v-switch>
            </v-flex> -->

            <v-layout align-end justify-end row fill-height>
              <v-flex text-xs-left>
                <span class="requeridTitles">{{$t('message.requiredFieldsMessage')}}</span>
              </v-flex>
              <v-btn
                @click="closeDialog()"
                dark
                :color="currentBussinesClub.theme.secondary"
              >{{$t('message.cancel')}}</v-btn>
              <v-btn
                @click="saveAddress()"
                dark
                :color="currentBussinesClub.theme.primary"
              >{{$t('message.save')}}</v-btn>
            </v-layout>
          </v-form>
        </v-container>
      </v-card>
    </v-dialog>
  </v-flex>
</template>

<script>
import Vue from "vue";

import { mapGetters } from "vuex";

import VueUploadMultipleImage from "vue-upload-multiple-image";

import ScheduleOffice from "./schedulesOffice";

import YovirtMap from "../../components/Map/yovirtMap";

import TelephoneInput from 'Components/TelephoneInput/vue-tel-input'

export default {
  components: {
    VueUploadMultipleImage,
    'v-telephone-input': TelephoneInput,
    "vue-schedule-office": ScheduleOffice,
    "v-yovirt-map":YovirtMap
  },
  created(){
    this.required = this.$t('message.required');
    this.maxLengthText = this.$t('message.maxLengthText');
    this.phoneRules = [
            v => !!v || this.required,
            v =>
            /^[0-9+]{2,40}$/.test(
                v
            ) || this.maxLengthText
        ]
   this.$nextTick(()=>{
     this.locationMap++;

   })
  },
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "coordinatesSelected",
      "businessAddresses"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    },
    coordinatesSelected: {
      get() {
        return this.$store.getters.coordinatesSelected;
      }
    },
    currentUserBusiness: {
      get() {
        if (this.$store.getters.currentUserBusiness != "")
          return this.$store.getters.currentUserBusiness;
      }
    }
  },
  data() {
    return {
      phoneRules:[],
      maxLengthTex: '',
      required: '',
      keyMap:0,
			locationMap: 0,
      radioGroup: 1,
      addressIndex: -1,
      addressDialog: false,
      office: {
        address: "",
        email: [],
        mapPosition: {
          latitude: "",
          longitude: ""
        },
        phones: [],
        schedule: [{}],
        photos: [],
        services: {},
        questions: {
          question1: false,
          question2: false,
          question3: false,
          question4: false,
          question5: false
        }
      },

      //Data
      images: [],
      prefix: "",
      emailContact: "",
      contactPhone: "",
      prefixPhone: "",
      number: "",
      position: {
        lat: -0.1689114860027461,
        lng: -78.46970841049801
      },
      latitud: -0.1689114860027461,
      longitud: -78.46970841049801,
      latLngRules: { required: v => !!v || "Coordenada requerido" },
      servicesSelected: [],
      servicesList: [
        "Parqueo",
        "Parqueo Gratuito",
        "Wifi",
        "Wifi gratis",
        "Wifi gratis",
        "Amigo de las mascotas",
        "Acepta tarjeta de crédito"
      ],
      /*Data use as v-model for storage address info */
    address: {
        emails: [],
        phones: [],
        address: {
            fullAddress: "",
            location: {
                latitude: 0,
                longitude: 0
            }
        },
        schedule: [],
        photos: [],
        services: [],
        mainAddress: false
    }

    };
  },
  methods: {
    //Methods to upload multiple images
    uploadImageSuccess(formData, index, fileList) {

      // Upload image api
      // axios.post('http://your-url-upload', { data: formData }).then(response => {
      // })
    },
    beforeRemove(index, done, fileList) {
      var r = confirm("remove image");
      if (r == true) {
        done();
      } else {
      }
    },
    editImage(formData, index, fileList) {

    },
    dataChange(data) {

    },
    //
    removeService(item) {
      this.servicesSelected.splice(this.servicesSelected.indexOf(item), 1);
      this.servicesSelected = [...this.servicesSelected];
    },
    closeAddressDialog() {
      this.addressDialog = false;
    },
    closeDialog() {

      this.locationMap=0;
      this.addressDialog = false;
    },
    /* Register a new business's address */
    saveAddress() {
      this.$store.dispatch("doAddBusinessAddress",{businessId: this.currentUserBusiness,address:this.address})
      .then(res=>{
        this.$store.dispatch("doNotification", {
            msg: this.$t('message.businessAddressAddSuccess'),
            type: "success"
          });
      })
      .catch(err=>{
        this.$store.dispatch("doNotification", {
            msg: this.$t('message.couldNotAddBusinessAddress'),
            type: "error"
          });
      })
      .finally(()=>{
          this.addressDialog = false;
          this.$refs.address.reset();
      })

    },
    setPlace(place) {
      this.position = {
        lat: place.geometry.location.lat(),
        lng: place.geometry.location.lng()
      };
      this.center = this.coordinatesSelected.location;
    },
    addMarkerClicked(e) {
      this.position = {
        lat: e.latLng.lat(),
        lng: e.latLng.lng()
      };
      this.center = this.position;
    },
    openAddressDialog(index) {
      
      this.$refs.map.test()
      this.addressIndex = index;
      this.addressDialog = true;
    },
    addEmail() {
      this.address.emails.push(this.emailContact);
      this.emailContact = "";
    },
    deleteEmail(index) {
      this.address.emails.splice(index, 1);
    },
    addPhone() {
      let newPhone = {
        prefix: this.prefix,
        number: this.number
      };
      this.address.phones.push(newPhone);
      this.prefix = "";
      this.number = "";
    },
    deletePhones(index) {
      this.address.phones.splice(index, 1);
    },
    onlyNumber($event) {
      var key = $event.keyCode ? $event.keyCode : $event.which;
      if (key < 48 || (key > 57 && key !== 46)) {
        $event.preventDefault();
      }
    },
    /* Bind data from storage into address v-model */
    setInfoAddress(index){

      this.address=this.businessAddresses[index];
    }
  }
};
</script>
<style>
.noContent {
  color: gray !important;
}
.borderCards {
  border-style: solid;
  border-width: 1px;
  border-color: #cfd8dc;
}
</style>
