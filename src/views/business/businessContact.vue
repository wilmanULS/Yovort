<template>
  <v-flex>
    <v-layout row wrap>
      <v-flex :class="$vuetify.breakpoint.smAndUp?'xs10 lg10 sm10 xl0':'xs12 lg12 sm12 xl12'">
        <span class="vb-cp-title">{{$t('message.vBusinessContact')}}</span>
        <span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessContactDetail')}}</span>
      </v-flex>
      <v-flex :class="$vuetify.breakpoint.smAndUp?'xs2 lg2 sm2 xl2':'xs12 lg12 sm12 xl12'">
        <v-btn
          dark
          small
          :class="$vuetify.breakpoint.smAndUp?'buttonTall':'buttonSmall'"
          :color="currentBussinesClub.theme.primary"
          @click="addNewContact('',2)"
        >{{$t('message.vBusinessContactAddNew')}}</v-btn>
      </v-flex>
      <v-flex v-if="readyContacts" xs12 sm12 md12 lg12 xl12 style="padding-top:padding-bottom: 15px;">
            <carousel
              class="wrapperBusinnesCarouselContacts"
              :style="$vuetify.breakpoint.smAndUp?'padding-left: 0px;padding-right: 0px;':'padding-left: 6px;padding-right: 5px;'"
              @touchmove="prevent"
              autoWidth
              :dots="false"
              :margin="20"
              :items="1"
              :navText="['<','>']"
            >
            <v-flex
                xs12
                lg12
                sm12
                md12
                xl12
                v-for="(address,index) in addresses"
                :key="index"
                :class="selectedCard==index?'containerBusinessContact containerBusinessContactActive':'containerBusinessContact'"
                >
                <v-flex style="height: 38px;cursor: default;" xs12 sm12 md12 lg12 xl12>
                  <v-layout wrap row>
                    <v-flex xs5 sm5 md5 lg5 xl5>
                      <span :style="'font-size: 14px;padding-left: 10px;color:'+currentBussinesClub.theme.primary">{{$t('message.vBusinessOpenNow')}}</span>
                    </v-flex>
                       
                    
                    <v-flex xs6 sm6 md6 lg6 xl6>
                         <v-layout
                          wrap
                          row
                        >
                          <v-flex xs9 sm9 md9 lg9 xl9 style="text-align: right;">
                            <span
                              :class="activateAux?'deactivateText':'activateText'"
                            >{{activateAux?$t('message.deactivate'):$t('message.activate')}}</span>
                          </v-flex>
                          <v-flex sm3 xs3 md3 lg3 xl3  style="padding-left: 19px;">
                            <v-switch
                              :class="activateAux?'switchActivate':'switchDeactivate'"
                              style="margin-top: 0px; padding-top: 0px;"
                              v-model="activateAux"
                              :color="currentBussinesClub.theme.primary"
                            ></v-switch>
                          </v-flex>
                  </v-layout>
                    </v-flex>
                  </v-layout>
                </v-flex>
                <v-layout style="cursor: pointer;"  @click="openDetailInfo(address.id,index)" row wrap>
                  <v-flex xs12 sm12 md12 lg12 xl12 class="businessName"><span>{{businessName}}</span></v-flex>
                  <v-flex xs12 sm12 md12 lg12 xl12 class="businessDetail"><p>{{getObjectPath(address, 'city','')+', '}}{{getCountry(getObjectPath(address, 'country','')) }}</p></v-flex>
                  <v-flex xs12 sm12 md12 lg12 xl12 class="businessDetail"><p>{{getObjectPath(address, 'fullAddress','')}}</p></v-flex>
                  <v-flex xs12 sm12 md12 lg12 xl12 class="businessDetailFooter"><p>{{$t('message.vBusinessCpMenuModifiedAt')+' '}}{{parseDate(getObjectPath(address, 'updatedAt',''))}}</p></v-flex>
                </v-layout>

            </v-flex>

            </carousel>
      </v-flex>
    </v-layout>
    <v-business-info-contact v-if="selectedCard>-1" v-bind:businessId="businessId" v-bind:addressId="addressId" ref="businessInfoContact"></v-business-info-contact>
  </v-flex>
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
import BusinessInfoContact from './businessInfoContact';
import moment from "moment";

export default {
  components: {
    "v-telephone-input": TelephoneInput,
    "v-yovirt-map": YovirtMap,
    "v-schedules-manager": SchedulesManager,
    "v-special-schedules-manager": SpecialSchedulesManager,
    "v-popup-image": popUpImage,
    "v-business-info-contact":BusinessInfoContact,
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
      "businessId",
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

  },
  created() {
    this.stringRules = [v => !!v || this.$t("message.required")];
  },
  mounted() {
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

    this.$store.dispatch("doGetBusinessAddresses", this.businessId).then((data)=>{
        this.addresses=data.data.data.addresses
        this.businessName=Vue.getObjectPath( data.data.data,'businessName','')
        if(this.addresses.length>1){
             this.addressId=Vue.getObjectPath(this.addresses[0],'id','');
        }
        console.log('address',this.addresses)

        /* this.addressId= */
    }).finally(()=>{
      this.readyContacts=true
    });
  },
  data() {
    return {
      selectedCard:-1,
      addressId:'',
      activateAux:false,//Delete after set activate atribute to address
      getObjectPath: Vue.getObjectPath,
      businessName: "",
      addresses:undefined,
      readyContacts:false,
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
      }
    };
  },
  methods: {
    addNewContact(addressId,action){
        let el=this;
        this.$nextTick(()=>{
         el.$refs.businessInfoContact.loadDetailAddress(addressId,action);
        })
    },
    parseDate(date){
      var date=new Date(date)
      return moment(date).format("DD/MM/YYYY HH:mm")
    },
    openDetailInfo(addressId,index){
        this.selectedCard=index;
        let el=this;
        this.$nextTick(()=>{
         el.$refs.businessInfoContact.loadDetailAddress(addressId,1);
        })
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
    openSpecialSchedulesGestor(){
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
  --secondary:#ffffff;
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
  background-color: white ;
}
.containerBusinessContact .v-input--switch__track{
  color: rgba(0,0,0,0.38) !important;
}
.containerBusinessContactActive{
  background-color: var(--primary) ;
}
.containerBusinessContactActive span{
  color: white !important
}
.containerBusinessContactActive .businessName span{
  color: white
}
.containerBusinessContactActive .businessDetail p{
  color: white
}
.containerBusinessContactActive .businessDetailFooter p{
  color: white
}
.containerBusinessContactActive .v-input--switch__track{
  color: white !important
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
.switchDeactivate .v-input--switch__thumb{
    color:var(--secondary) !important;
}
.switchActivate .v-input--switch__thumb{
    color: var(--primary) !important;
}

.activateText{
  font-size: 14px;
  color:var(--secondary) !important;
}
.deactivateText{
  font-size: 14px;
  color:#bbbbbb !important;
}
.businessName{
    margin-bottom: 15px;
    text-align: center;
    font-size: 23px;
    transform: scale(0.9,1.4);
}
.businessName span{
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
 .businessDetail p{   
  color: rgb(165, 166, 167);
 }
 .businessDetailFooter{    
    margin-top: 22px;
    text-align: center;
    font-size: 14px;    
 }  
 .businessDetailFooter p{   
  color: rgb(165, 166, 167);
 }
/* 
Carousel style contacts */
.wrapperBusinnesCarouselContacts  .owl-prev {
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
.wrapperBusinnesCarouselContacts  .owl-next {
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

.wrapperBusinnesCarouselContacts  .owl-carousel {
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


 