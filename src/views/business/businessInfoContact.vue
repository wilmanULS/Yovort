<template>
  <v-form v-if="ready" v-model="valid" ref="formAddress" autocomplete="off">
    <v-flex pt-3>
      <v-layout row wrap>
        <v-flex :class="$vuetify.breakpoint.smAndUp?'xs10 lg10 sm10 xl0':'xs12 lg12 sm12 xl12'">
          <span class="vb-cp-title">{{$t('message.vBusinessContactAddNewHeader')}}</span>
          <span
            class="grey--text vb-cp-subtitle"
          >{{$t('message.vBusinessContactAddNewHeaderDetail')}}</span>
        </v-flex>
        <v-flex :class="$vuetify.breakpoint.smAndUp?'xs2 lg2 sm2 xl2':'xs12 lg12 sm12 xl12'">
          <v-switch
            :label="$t('message.hasPhisicLocal')"
            v-model="phisicalLocal"
            :color="currentBussinesClub.theme.primary"
            hide-details
          ></v-switch>
        </v-flex>
      </v-layout>

      <v-layout row wrap style="heigth: 100%!important;">
        <v-flex xs12 md6>
          <v-city-address-picker
            ref="addressPicker"
            :country="intCountry"
            :province="intProvince"
            :city="intCity"
            @onValueChange="changeAddress"
          ></v-city-address-picker>

          <v-flex
            v-if="phisicalLocal"
            xs12
            :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}"
          >
            <v-text-field
              :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
              clearable
              hide-details
              :color="currentBussinesClub.theme.primary"
              v-bind:label="$t('message.vBusinessAddressPostal')+' (*)'"
              ref="postalAddress"
              v-model="fullAddress"
              required
              :rules="stringRules"
            ></v-text-field>
          </v-flex>
          <v-flex
            v-if="phisicalLocal"
            xs12
            :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}"
          >
            <v-text-field
              :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
              hide-details
              clearable
              required
              :color="currentBussinesClub.theme.primary"
              v-bind:label="$t('message.vBusinessCodePostal')+' (*)'"
              v-model="postalCode"
              ref="postalCode"
              :rules="stringRules"
            ></v-text-field>
          </v-flex>
        </v-flex>

        <v-flex xs12 md6>
          <v-flex xs12 v-if="phisicalLocal">
            <span
              :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
              class="grey--text"
            >
              <strong>{{$t('message.vBusinessWhereIs')}}</strong>
            </span>
          </v-flex>
          <v-flex xs12 v-if="phisicalLocal">
            <v-yovirt-map
              :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
              ref="map"
              style="border:1px solid var(--primary);margin-right:20px!important"
            ></v-yovirt-map>
          </v-flex>
          <v-flex xs12 v-if="!phisicalLocal">
            <v-layout row wrap>
              <v-flex
                xs12
                md12
                style="padding-bottom:31px !important;"
                :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}"
              >
                <v-flex
                  style="border: 1px solid;margin-left: 0px !important;margin-right: 1rem !important;"
                  :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
                >
                  <v-layout row wrap>
                    <v-flex xs12 class="profile-flex-padding">
                      <v-layout row wrap>
                        <v-flex xs12>
                          <v-telephone-input
                            ref="number"
                            :disabledFetchingCountry="true"
                            v-model="number"
                            :color="currentBussinesClub.theme.primary"
                            :placeholder="$t('message.vBusinessStepAdditionalInfoPhones')"
                            hide-details
                            :required="true"
                            :appendIcon="'check'"
                            @onInput="onInput"
                            @onEnter="addNewPhone"
                            :preferredCountries="['ec']"
                          ></v-telephone-input>
                        </v-flex>
                      </v-layout>
                    </v-flex>
                    <v-flex xs12 md12 lg12 pt-2>
                      <v-card-text style="padding:0px!important">
                        <v-container pa-0>
                          <template>
                            <v-layout row wrap>
                              <div v-for="(phone, index) in addressModel.phones" :key="index">
                                <v-chip
                                  style="background: white!important;"
                                  outline
                                  :color="currentBussinesClub.theme.primary"
                                  close
                                  @input="RemovePhoneNumber(index)"
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
              <v-flex xs12 md12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
                <v-flex
                  :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
                  style="border: 1px solid "
                >
                  <v-layout row wrap>
                    <v-flex xs12 md12 class="profile-flex-padding">
                      <v-text-field
                        style="margin-bottom:0px!important"
                        :label="$t('message.emailContact')"
                        :color="currentBussinesClub.theme.primary"
                        class="mb-1"
                        clearable
                        append-icon="check"
                        @click:append="addNewEmail"
                        @keyup.enter="addNewEmail"
                        v-model="newEmail"
                        name="name"
                        ref="emailAdd"
                      ></v-text-field>
                    </v-flex>
                  </v-layout>
                  <v-flex xs12 md12 lg12>
                    <v-card-text style="padding:0px!important">
                      <v-container pa-0>
                        <template>
                          <v-layout row wrap>
                            <div v-for="(email, index) in addressModel.emails" :key="index">
                              <v-chip
                                style="background: white!important;"
                                outline
                                :color="currentBussinesClub.theme.primary"
                                close
                                @input="RemoveEmail(index)"
                              >{{email}}</v-chip>
                            </div>
                          </v-layout>
                        </template>
                      </v-container>
                    </v-card-text>
                  </v-flex>
                </v-flex>
              </v-flex>
            </v-layout>
          </v-flex>
          <v-flex text-xs-left text-md-left pl-1 xs12></v-flex>
        </v-flex>
      </v-layout>

      <v-layout row wrap v-if="phisicalLocal">
        <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
          <v-flex
            :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
            style="border: 1px solid "
          >
            <v-layout row wrap>
              <v-flex xs12 md12 class="profile-flex-padding">
                <v-text-field
                  style="margin-bottom:0px!important"
                  :label="$t('message.emailContact')"
                  :color="currentBussinesClub.theme.primary"
                  class="mb-1"
                  clearable
                  append-icon="check"
                  @click:append="addNewEmail"
                  @keyup.enter="addNewEmail"
                  v-model="newEmail"
                  name="name"
                  ref="emailAdd"
                ></v-text-field>
              </v-flex>
            </v-layout>
            <v-flex xs12 md12 lg12>
              <v-card-text style="padding:0px!important">
                <v-container pa-0>
                  <template>
                    <v-layout row wrap>
                      <div v-for="(email, index) in addressModel.emails" :key="index">
                        <v-chip
                          style="background: white!important;"
                          outline
                          :color="currentBussinesClub.theme.primary"
                          close
                          @input="RemoveEmail(index)"
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
          <v-flex
            style="border: 1px solid "
            :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          >
            <v-layout row wrap>
              <v-flex xs12 class="profile-flex-padding">
                <v-layout row wrap>
                  <v-flex xs12>
                    <v-telephone-input
                      ref="number"
                      :disabledFetchingCountry="true"
                      v-model="number"
                      :color="currentBussinesClub.theme.primary"
                      :placeholder="$t('message.vBusinessStepAdditionalInfoPhones')"
                      hide-details
                      :required="true"
                      :appendIcon="'check'"
                      @onInput="onInput"
                      @onEnter="addNewPhone"
                      :preferredCountries="['ec']"
                    ></v-telephone-input>
                  </v-flex>
                </v-layout>
              </v-flex>
              <v-flex xs12 md12 lg12 pt-2>
                <v-card-text style="padding:0px!important">
                  <v-container pa-0>
                    <template>
                      <v-layout row wrap>
                        <div v-for="(phone, index) in addressModel.phones" :key="index">
                          <v-chip
                            style="background: white!important;"
                            outline
                            :color="currentBussinesClub.theme.primary"
                            close
                            @input="RemovePhoneNumber(index)"
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
      <v-layout wrap row>
        <v-flex xs12 sm12 md6 lg6 xl6>
          <v-layout wrap row>
            <v-flex
              style="height: height: 130px;"
              :class="$vuetify.breakpoint.smAndUp?'xs4 lg4 sm4 xl4':'xs6 lg6 sm6 xl6'"
            >
              <v-flex
                class="cardServices"
                @click="openSchedulesGestor"
                :class="$vuetify.breakpoint.smAndUp?'marginsTall':'marginsSmallRigth'"
              >
                <v-layout wrap row>
                  <v-flex xs12 lg12 sm12 md12 xl12>
                    <span>{{$t('message.vBusinessGlobalSchedules')}}</span>
                  </v-flex>
                  <v-flex style="margin-top: 10px;" xs12 lg12 sm12 md12 xl12>
                    <v-icon :color="currentBussinesClub.theme.primary">mdi mdi-calendar-clock</v-icon>
                  </v-flex>
                </v-layout>
              </v-flex>
            </v-flex>
            <v-flex
              style="height: height: 130px;"
              :class="$vuetify.breakpoint.smAndUp?'xs4 lg4 sm4 xl4':'xs6 lg6 sm6 xl6'"
            >
              <v-flex
                @click="openSpecialSchedulesGestor"
                class="cardServices"
                :class="$vuetify.breakpoint.smAndUp?'marginsTall':'marginSmallLeft'"
              >
                <v-layout wrap row>
                  <v-flex xs12 lg12 sm12 md12 xl12>
                    <span>{{$t('message.vBusinessSpecialSchedules')}}</span>
                  </v-flex>
                  <v-flex style="margin-top: 10px;" xs12 lg12 sm12 md12 xl12>
                    <v-icon :color="currentBussinesClub.theme.primary">mdi mdi-calendar-star</v-icon>
                  </v-flex>
                </v-layout>
              </v-flex>
            </v-flex>
            <v-flex
              style="height: height: 130px;"
              :class="$vuetify.breakpoint.smAndUp?'xs4 lg4 sm4 xl4':'xs12 pt-3'"
            >
              <v-flex
                :class="$vuetify.breakpoint.smAndUp?'marginsTallLastItem':'xs6 centerDiv'"
                class="cardServices"
              >
                <v-layout wrap row>
                  <v-flex xs12 lg12 sm12 md12 xl12>
                    <span>{{$t('message.services')}}</span>
                  </v-flex>
                  <v-flex style="margin-top: 10px;" xs12 lg12 sm12 md12 xl12>
                    <v-icon :color="currentBussinesClub.theme.primary">mdi mdi-hand-okay</v-icon>
                  </v-flex>
                </v-layout>
              </v-flex>
            </v-flex>
          </v-layout>
        </v-flex>
        <v-flex
          xs12
          sm12
          md6
          lg6
          xl6
          :style="$vuetify.breakpoint.smAndDown?'padding-left: 0px;padding-right: 18px;padding-top: 18px;':'    padding-left: 16px;padding-right: 0px;padding-top: 0px;'"
        >
          <v-flex
            xs12
            sm12
            md12
            lg12
            xl12
            style="
                border: 1px solid;
                height: 134px;
                width: 100%;
                padding-left: 13px;
                padding-right: 14px;"
          >
            <v-flex xs12 sm12 lg12 md12 style="margin-top: -2px;">
              <span
                style="color: rgba(0, 0, 0, 0.54);font-size: 14px;padding-left: 2px;"
              >{{$t('message.vBusinessUploadImageLocation')+' ('+$t('message.to').toLowerCase() +' 10)'}}</span>
            </v-flex>
            <carousel
              class="wrapperBusinnesCarouselImage"
              :style="$vuetify.breakpoint.smAndUp?'padding-left: 0px;padding-right: 0px;':'padding-left: 6px;padding-right: 5px;'"
              @touchmove="prevent"
              autoWidth
              :dots="false"
              :margin="20"
              :items="1"
              :navText="['<','>']"
            >
              <v-flex
                v-for="(uploadImage,index) in uploadImages"
                :key="index"
                class="containerUploadFilesBusiness"
                :style="!uploadImage.imageUrl?'cursor:pointer;background-image:url(/static/img/background-upload-images-business.jpg);background-repeat: no-repeat;background-size: 150px 90px;':'cursor:default'"
                @click="!uploadImage.imageUrl?pickFile('image'+index):''"
              >
                <v-flex class="businessHoverCard">
                  <v-icon
                    v-if="!uploadImage.imageUrl"
                    class="iconUploadFileBusiness"
                    :color="currentBussinesClub.theme.primary"
                  >mdi mdi-cloud-upload</v-icon>

                  <v-flex class="containerImageFile">
                    <v-img
                      :src="uploadImage.imageUrl"
                      style="max-width: 100%;max-height: 98%;border-radius: 5px;"
                      v-if="uploadImage.imageUrl"
                    ></v-img>
                  </v-flex>
                  <div v-if="uploadImage.imageUrl" class="card">
                    <div class="card-content">
                      <div class="content">
                        <v-layout wrap row style="margin: 21% 0 0 0;">
                          <v-flex md4 sm4 xs4 lg4 xl4>
                            <v-icon
                              style="cursor:pointer"
                              :title="$t('message.preview')"
                              @click="openImageDialog(uploadImage.imageUrl)"
                              color="white"
                            >mdi mdi-eye</v-icon>
                          </v-flex>
                          <v-flex md4 sm4 xs4 lg4 xl4>
                            <v-icon
                              style="cursor:pointer"
                              :title="$t('message.edit')"
                              @click="pickFile('image'+index)"
                              color="white"
                            >mdi mdi-cloud-upload</v-icon>
                          </v-flex>
                          <v-flex md4 sm4 xs4 lg4 xl4>
                            <v-icon
                              style="cursor:pointer"
                              :title="$t('message.delete')"
                              @click="deleteUploadImage(index)"
                              color="white"
                            >mdi mdi-delete</v-icon>
                          </v-flex>
                        </v-layout>
                      </div>
                    </div>
                  </div>
                  <input
                    type="file"
                    style="display: none"
                    :ref="'image'+index"
                    accept="image/*"
                    @change="onFilePicked('image'+index,$event,index)"
                  >
                </v-flex>
              </v-flex>
            </carousel>
          </v-flex>
        </v-flex>
      </v-layout>
    </v-flex>
    <v-schedules-manager ref="schedulesManager"></v-schedules-manager>
    <v-special-schedules-manager ref="specialSchedulesManager"></v-special-schedules-manager>
    <v-popup-image
      @onCancel="closeImageDialog()"
      ref="imageDialog"
      :imageName="imageInDialog"
      :image="imageInDialog"
      :colorBackground="currentBussinesClub.theme.primary"
    ></v-popup-image>
  </v-form>
</template>

<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import AppConfig from "Constants/AppConfig";
import confirmDialog from "../../components/ySocialHome/confirmDialog";
import TelephoneInput from "Components/TelephoneInput/vue-tel-input";
import YovirtMap from "../../components/Map/yovirtMap";
import carousel from "vue-owl-carousel";
import SchedulesManager from "./schedulesManager";
import SpecialSchedulesManager from "./specialSchedulesManager";
import popUpImage from "./popUpImage";
import CityAddressPicker from "./addressPicker";

export default {
  props: {
    addressId: String,
    businessId: String,
    action:Number  
  },
  components: {
    "v-telephone-input": TelephoneInput,
    "v-yovirt-map": YovirtMap,
    "v-schedules-manager": SchedulesManager,
    "v-special-schedules-manager": SpecialSchedulesManager,
    "v-popup-image": popUpImage,
    "v-city-address-picker": CityAddressPicker,
    carousel
  },
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "getCountry"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    },
    atEndOfList() {
      return (
        this.currentOffset <=
        this.paginationFactor * -1 * (this.items.length - this.windowSize)
      );
    },
    atHeadOfList() {
      return this.currentOffset === 0;
    }
  },
  watch: {
    ready(val) {
      if (!val) {
        this.$el.style.setProperty(
          "--colorButtom",
          this.currentBussinesClub.theme.primary
        );
        this.$el.style.setProperty(
          "--primary",
          this.currentBussinesClub.theme.primary
        );

        this.$el.style.setProperty(
          "--secondary",
          this.currentBussinesClub.theme.secondary
        );
      }
    }
  },
  created() {
    this.stringRules = [v => !!v || this.$t("message.required")];
  },
  mounted() {
    /*  if(this.addressId!=''){
              let data={
          businessId: this.businessId,
          addressId: this.addressId
      }
      this.$store
        .dispatch("doGetBusinessAddress", data)
        .then(data => {
          this.addressData=data.data.data
          console.log(data)
          this.$refs.addressPicker.clearFields();
        })
        .finally(() => {
            this.setDataToFields();
        });
      } */
  },
  data() {
    return {
      ready: false,
      valid: false,
      activateAux: false, //Delete after set activate atribute to address
      getObjectPath: Vue.getObjectPath,
      businessName: "",
      addresses: undefined,
      readyContacts: false,
      imageInDialog: "",
      uploadImages: [
        {
          imageFile: "",
          imageName: "",
          imageUrl: ""
        },
        {
          imageFile: "",
          imageName: "",
          imageUrl: ""
        },
        {
          imageFile: "",
          imageName: "",
          imageUrl: ""
        },
        {
          imageFile: "",
          imageName: "",
          imageUrl: ""
        },
        {
          imageFile: "",
          imageName: "",
          imageUrl: ""
        },
        {
          imageFile: "",
          imageName: "",
          imageUrl: ""
        },
        {
          imageFile: "",
          imageName: "",
          imageUrl: ""
        },
        {
          imageFile: "",
          imageName: "",
          imageUrl: ""
        },
        {
          imageFile: "",
          imageName: "",
          imageUrl: ""
        },
        {
          imageFile: "",
          imageName: "",
          imageUrl: ""
        }
      ],
      fullAddress: "",
      loadingCountries: false,
      intCountry: null,
      provinceLoading: false,
      readOnlyProvince: false,
      intProvince: null,
      cities: [],
      cityLoading: false,
      readOnlyCity: false,
      intCity: null,
      stringRules: [],
      postalCode: "",
      phisicalLocal: true,
      currentOffset: 0,
      windowSize: 3,
      paginationFactor: 220,
      slides: 10,
      phoneValid: "",
      newEmail: "",
      prefix: "",
      number: "",
      addressModel: {
        emails: [],
        phones: []
      },
      addressData: undefined
    };
  },
  methods: {
    setDataToFields() {
      this.intCountry = Vue.getObjectPath(
        this.addressData.address,
        "country",
        undefined
      );
      this.intProvince = Vue.getObjectPath(
        this.addressData.address,
        "province",
        undefined
      );
      this.intCity = Vue.getObjectPath(
        this.addressData.address,
        "city",
        undefined
      );
      this.phisicalLocal = Vue.getObjectPath(
        this.addressData.address,
        "localStatus",
        true
      );
      this.fullAddress = Vue.getObjectPath(
        this.addressData.address,
        "fullAddress",
        undefined
      );
      this.postalCode = Vue.getObjectPath(
        this.addressData.address,
        "postalCode",
        undefined
      );
      this.postalCode = Vue.getObjectPath(
        this.addressData.address,
        "postalCode",
        undefined
      );
    },
    loadDetailAddress(id,action) {
      let el = this;
      this.ready = true;
      this.$nextTick(() => {
        switch (action) {
          case 1:
             this.loadData(id)
            break;
        
          case 2:
             this.newAddressData()
            break;
        }
       
      });
    },
    loadData(id){
      let el=this
       el.$refs.formAddress.reset();
        let data = {
          businessId: el.businessId,
          addressId: id
        };
        el.$store
          .dispatch("doGetBusinessAddress", data)
          .then(data => {
            el.addressData = data.data.data;
            console.log(el.addressData);
            el.$refs.addressPicker.clearFields();
          })
          .finally(() => {
            el.setDataToFields();
            el.$refs.addressPicker.changeValues(
              el.intCountry,
              el.intProvince,
              el.intCity
            );
          });
    },
    newAddressData(){
      this.intCountry = undefined;
      this.intProvince = undefined;
      this.intCity = undefined;
      this.phisicalLocal = undefined;
      this.fullAddress = undefined;
      this.postalCode = undefined;
      this.postalCode = undefined;
    },
    openImageDialog(imageUrl) {
      this.imageInDialog = imageUrl;
      this.$refs.imageDialog.openDialog();
    },
    closeImageDialog() {
      this.$refs.imageDialog.close();
    },
    pickFile(ref) {
      this.$refs[ref][0].click();
    },
    changeAddress(payload) {
      this.intCountry = payload.country;
      this.intProvince = payload.province;
      this.intCity = payload.city;
    },
    onFilePicked(ref, e, index) {
      const files = e.target.files;
      if (files[0] !== undefined) {
        this.uploadImages[index].imageName = files[0].name;
        if (this.uploadImages[index].imageName.lastIndexOf(".") <= 0) {
          return;
        }
        const fr = new FileReader();
        fr.readAsDataURL(files[0]);
        fr.addEventListener("load", () => {
          this.uploadImages[index].imageUrl = fr.result;
          this.uploadImages[index].imageFile = files[0]; // this is an image file that can be sent to server...
        });
      } else {
        /* this.uploadImages[index].imageName = ''
					this.uploadImages[index].imageFile = ''
					this.uploadImages[index].imageUrl = '' */
      }
    },
    deleteUploadImage(index) {
      this.uploadImages[index].imageName = "";
      this.uploadImages[index].imageFile = "";
      this.uploadImages[index].imageUrl = "";
    },
    //Prevent touch on phones
    prevent(event) {
      event.preventDefault();
      event.stopPropagation();
    },
    openSchedulesGestor() {
      this.$refs.schedulesManager.openDialog();
    },
    openSpecialSchedulesGestor() {
      this.$refs.specialSchedulesManager.openDialog();
    },
    moveCarousel(direction) {
      // Find a more elegant way to express the :style. consider using props to make it truly generic
      if (direction === 1 && !this.atEndOfList) {
        this.currentOffset -= this.paginationFactor;
      } else if (direction === -1 && !this.atHeadOfList) {
        this.currentOffset += this.paginationFactor;
      }
    },
    /**
     * Add new contact email for previous main addresses
     */
    addNewEmail() {
      /* Check if the email exists as data on control */
      if (!this.newEmail || this.newEmail.length === 0) {
        return 0;
      }
      //this.newEmail=this.newEmail.toLowerCase();
      /* Check if the email is valid */
      if (
        !/^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
          this.newEmail
        )
      ) {
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.errorMessageWrongEmail"),
          type: "error"
        });
        return -1;
      }
      /* Check if the email was added previously */
      const emailList = this.addressModel.emails.filter(
        value => value.toLowerCase() === this.newEmail.toLowerCase()
      );
      if (emailList && emailList.length > 0) {
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.errorMessageEmailAlreadyExists"),
          type: "error"
        });
        return -1;
      }
      this.addressModel.emails.push(this.newEmail);
      /* Clear values */
      this.newEmail = "";
      return 0;
    },
    /**
     * Remove an email address
     */
    RemoveEmail(index) {
      this.addressModel.emails.splice(index, 1);
    },
    RemovePhoneNumber(index) {
      this.addressModel.phones.splice(index, 1);
    },

    /**
     * Add new phone to the current job
     */
    addNewPhone() {
      /* Check if the phone number exists */
      if (!this.number || this.number.length === 0) {
        return 0;
      }

      /* Check if the phone number is valid */
      if (!this.phoneValid) {
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.errorMessageWrongPhone"),
          type: "error"
        });
        return -1;
      }

      /* Check if the phone was added previously */
      const phones = this.addressModel.phones.filter(
        value => value.number === this.number
      );
      if (phones && phones.length > 0) {
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.errorMessagePhoneInUse"),
          type: "error"
        });
        return -1;
      }

      /* Add the phone to the list */
      this.addressModel.phones.push(this.number);

      /* Clear values */
      this.number = "";
      this.phoneValid = false;
      return 0;
    },
    /**
     * Event triggered on phone number change
     */
    onInput(payload) {
      this.phoneValid = payload.isValid;
      this.number = payload.number;
    }
  }
};
</script>
<style>
:root {
  --colorButtom: #ffffff;
  --primary: #ffffff;
  --secondary: #ffffff;
}
.iconUploadFileBusiness {
  display: block;
  margin-left: auto;
  margin-right: auto;
  margin-top: 40px;
  font-size: 40px;
}
.containerUploadFilesBusiness {
  height: 90px;
  width: 150px;
  margin-right: 0px;
  /*  display:inline-block; */
  text-align: center;
  border: 1px solid var(--colorButtom) !important;
  float: left;
  border-radius: 5px;
}
.containerBusinessContact {
  height: 190px;
  width: 320px;
  margin-right: 0px;
  /*  display:inline-block; */
  border: 1px solid;
  float: left;
  cursor: pointer;
}
.containerImageFile {
  height: 90px;
  width: 150px;
  border-radius: 5px;
}
.centerDiv {
  margin: 0 auto;
  padding: 10px;
  position: relative;
}
.marginsSmallRigth {
  margin-right: 7.5px;
}
.marginSmallLeft {
  margin-left: 7.5px;
}
.marginsTall {
  margin-right: 15px;
}
.marginsTallLastItem {
  margin-right: 17px;
}
.buttonTall {
  margin: 6px 8px;
}
.buttonSmall {
  margin: 6px 0px;
}
.switchDeactivate .v-input--switch__thumb {
  color: var(--secondary) !important;
}
.switchActivate .v-input--switch__thumb {
  color: var(--primary) !important;
}

.activateText {
  font-size: 14px;
  color: var(--secondary) !important;
}
.deactivateText {
  font-size: 14px;
  color: #bbbbbb !important;
}
.businessName {
  margin-bottom: 15px;
  text-align: center;
  font-size: 23px;
  transform: scale(0.9, 2);
}
.businessName span {
  color: rgb(205, 205, 206);
  text-transform: uppercase;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.businessDetail {
  text-align: center;
  font-size: 14px;
  margin-bottom: -13px;
}
.businessDetail p {
  color: rgb(165, 166, 167);
}
.businessDetailFooter {
  margin-top: 22px;
  text-align: center;
  font-size: 14px;
}
.businessDetailFooter p {
  color: rgb(165, 166, 167);
}
/* 
Carousel style contacts */
.wrapperBusinnesCarouselContacts .owl-prev {
  background-color: transparent !important;
  border-radius: 50% !important;
  width: 30px !important;
  height: 30px !important;
  border: none;
  padding-top: 1px !important;
  padding-right: 8px !important;
  padding-left: 3px !important;
  position: absolute;
  top: 38%;
  left: -9px;
  transform: scale(1.3, 2.5);
  color: var(--colorButtom) !important;
  line-height: 19px;
  font-size: 47px !important;
}
.wrapperBusinnesCarouselContacts .owl-next {
  background-color: transparent !important;
  border-radius: 50% !important;
  width: 30px !important;
  height: 30px !important;
  border: none;
  padding-top: 1px !important;
  padding-left: 3px !important;
  position: absolute;
  top: 38%;
  right: 10px;
  transform: scale(1.3, 2.5);
  color: var(--colorButtom) !important;
  line-height: 19px;
  font-size: 47px !important;
}

.wrapperBusinnesCarouselContacts .owl-carousel {
  -ms-touch-action: pan-y !important;
  touch-action: pan-y !important;
  padding-left: 35px;
  padding-right: 44px;
  z-index: 0;
}
/* 
Carousel style image */
.wrapperBusinnesCarouselImage .owl-prev {
  background-color: transparent !important;
  border-radius: 50% !important;
  width: 30px !important;
  height: 30px !important;
  border: none;
  padding-top: 1px !important;
  padding-right: 8px !important;
  padding-left: 3px !important;
  position: absolute;
  top: 33%;
  left: -28px;
  transform: scale(0.5, 1.5);
  color: var(--colorButtom) !important;
  line-height: 19px;
  font-size: 48px !important;
}

.wrapperBusinnesCarouselImage .owl-next {
  background-color: transparent !important;
  border-radius: 50% !important;
  width: 30px !important;
  height: 30px !important;
  border: none;
  padding-top: 1px !important;
  padding-left: 3px !important;
  position: absolute;
  top: 33%;
  right: -24px;
  transform: scale(0.5, 1.5);
  color: var(--colorButtom) !important;
  line-height: 19px;
  font-size: 48px !important;
}

.wrapperBusinnesCarouselImage .owl-carousel {
  -ms-touch-action: pan-y !important;
  touch-action: pan-y !important;
  padding-left: 3px;
  padding-right: 3px;
  z-index: 0;
}

.cardServices {
  border: 1px solid;
  cursor: pointer;
}
.cardServices span {
  color: rgba(0, 0, 0, 0.54);
  font-size: 14px;
  padding-left: 5px;
}
.cardServices i {
  color: rgba(0, 0, 0, 0.54);
  font-size: 80px;
}

/* Hover Style */
.businessHoverCard {
  width: 100%;
  margin-bottom: 30px;
}
.businessHoverCard .card {
  position: relative;
  text-align: left;
  width: auto;
  height: 90px;
  margin: 0px;
}
.businessHoverCard .card .cat-flag {
  position: absolute;
  top: 8px;
  left: -5px;
  z-index: 2;
  font-size: 13px;
  font-weight: 400;
  padding: 3px 5px 3px 5px;
  color: #fff;
  border-top-right-radius: 3px;
  border-bottom-right-radius: 3px;
  background-color: #2266aa;
}
.businessHoverCard .card .cat-flag:before,
.businessHoverCard .card .cat-flag:after {
  content: "";
  display: block;
  position: absolute;
  right: 100%;
  border-right: 10px solid #2266aa;
}
.businessHoverCard .card .cat-flag:before {
  top: 0;
  border-bottom: 13px solid transparent;
  border-top: 0 solid transparent;
}
.businessHoverCard .card .cat-flag:after {
  bottom: 0;
  border-top: 13px solid transparent;
  border-bottom: 0 solid transparent;
}
.businessHoverCard .card .card-img {
  text-align: center;
  background-color: #000;
}
.businessHoverCard .card .card-img img {
  transition: transform 0.7s;
  opacity: 0.8;
}
.businessHoverCard .card:before {
  content: "";
  position: absolute;
  bottom: 0;
  right: 0;
  left: 0;
  z-index: 1;
  height: 70px;
  display: block;
  filter: progid:DXImageTransform.Microsoft.gradient( startColorstr="#00000000", endColorstr="#000000", GradientType=0 );
  /* IE6-9 */
}
.businessHoverCard .card .card-content {
  border-radius: 3px;
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 3;
  overflow: hidden;
}
.businessHoverCard .card .card-content .content {
  transition: transform 0.4s;
  transform: translateY(100%);
  /* Permalink - use to edit and share this gradient: http://colorzilla.com/gradient-editor/#000000+0,000000+100&0+0,1+100 */
  background: -moz-linear-gradient(top, #000000ab, rgba(0, 0, 0, 0.781) 100%);
  /* FF3.6-15 */
  background: -webkit-linear-gradient(
    top,
    #000000ab,
    rgba(0, 0, 0, 0.781) 100%
  );
  /* Chrome10-25,Safari5.1-6 */
  background: linear-gradient(to bottom, #000000ab, rgba(0, 0, 0, 0.781) 100%);
  /* W3C, IE10+, FF16+, Chrome26+, Opera12+, Safari7+ */
  filter: progid:DXImageTransform.Microsoft.gradient( startColorstr="#00000000", endColorstr="#000000", GradientType=0 );
  /* IE6-9 */
  height: 180px;
}
.businessHoverCard .card .card-content .title {
  color: #fff;
  font-weight: 600;
  font-size: 24px;
  transform: translateY(-100%);
  transition: transform 0.4s;
  margin-top: 0;
  margin-bottom: 0;
  padding: 0 15px 15px;
}
.businessHoverCard .card .card-content .description {
  padding: 0 15px;
  color: #fff;
  font-size: 13px;
  margin: 0;
}
.businessHoverCard .card:hover .card-content .content {
  padding-bottom: 15px;
  transform: translateY(0);
}
.businessHoverCard .card:hover .card-content .title {
  transform: translateY(0);
}
</style>
