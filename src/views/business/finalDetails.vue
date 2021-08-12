<template>
<div id="final">
  <v-form v-model="valid" ref="form" >
    <v-layout row wrap style="heigth: 100%!important;">
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-text-field
          ref="navigationID"
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
          hide-details
          clearable
          required
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.vBusinessStepFinalNavigationID')"
          v-model="navigationId"
          :rules="nameRules"
          @change="setUrlPreview()"
        ></v-text-field>
      </v-flex>
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-text-field
          ref="navigationPreview"
          hide-details
          required
          :readonly="true"
          v-model="preview"
          :color="currentBussinesClub.theme.primary"
          :label="$t('message.vBusinessStepFinalUrlPreview')"
          :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
        ></v-text-field>
      </v-flex>
    </v-layout>
    <v-layout row wrap style="heigth: 100%!important;">
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-select
          ref="hasImageLogo"
          :color="currentBussinesClub.theme.primary"
          :label="$t('message.vBusinessStepFinalHasLogo')"
          attach
          item-text="name"
          item-value="code"
          hide-details
          :items="owningAnswers"
          v-model="hasImage"
          required
          :rules="nameRules"
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
        ></v-select>
      </v-flex>
      <v-flex
        xs12
        md6
        v-if="showImage"
        :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}"
      >
        <div :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }">
          <v-btn
            :color="currentBussinesClub.theme.primary"
            class="rounded-buttons"
            dark
            @click="pickFile"
            style="margin-bottom:0px!important; margin-left:0px!important"
          >{{$t('message.vBusinessStepFinalUploadLogo')}}</v-btn>
        </div>
      </v-flex>
    </v-layout>
    <v-layout row wrap style="heigth: 100%!important;" v-if="showImage">
      <v-flex xs12 md6>
        <v-layout row wrap>
          <v-flex xs12 md6>
            <span style="padding-top:20px;" class="grey--text">Color Primario (*)</span>
          </v-flex>
          <v-flex xs12 md6>
            <span style="padding-top:20px" class="grey--text">Color Secundario (*)</span>
          </v-flex>
        </v-layout>
        <v-layout row wrap>
          <v-flex xs12 md6>
            <div class="input-group color-picker" ref="primaryColorPicker">
              <input type="text" class="form-control" v-model="primaryColorValue">
              <span
                class="input-group-addon color-picker-container"
                v-bind:style="{'background-color':primaryColorValue+'!important'}"
              >
                <span class="current-color" :style="{'background-color': primaryColorValue}"></span>
              </span>
              <span class="input-group-addon color-picker-container">
                <span @click="togglePicker(1)">
                  <i style="color:black" class="mdi mdi-eyedropper-variant"></i>
                </span>
                <chrome-picker
		  :key="1"
                  :value="primaryColors"
                  @input="updateFromPrimaryPicker"
                  v-if="displayPrimaryColorPicker"
                />
              </span>
            </div>
          </v-flex>
          <v-flex xs12 md6>
            <div class="input-group color-picker" ref="secondaryColorPicker">
              <input type="text" class="form-control" v-model="secondaryColorValue">
              <span
                class="input-group-addon color-picker-container"
                v-bind:style="{'background-color':secondaryColorValue+'!important'}"
              >
                <span class="current-color" :style="'background-color: ' + secondaryColorValue"></span>
              </span>
              <span class="input-group-addon color-picker-container">
                <span @click="togglePicker(2)">
                  <i style="color:black" class="mdi mdi-eyedropper-variant"></i>
                </span>
                <chrome-picker
		  :key="2"
                  :value="secondaryColors"
                  @input="updateFromSecondaryPicker"
                  v-if="displaySecondaryColorPicker"
                />
              </span>
            </div>
          </v-flex>
        </v-layout>
      </v-flex>
      <v-flex xs12 md6>
        <v-layout row wrap fill-height justify-center align-center>
          <v-flex xs12 style="height:100%!important">
            <v-layout row wrap justify-center align-center>
              <v-hover>
                <v-img
                  :src="imageUrl"
                  style="border: 1px solid var(--primary); max-width:104px!important; height:104px!important"
                  v-if="imageUrl"
                  slot-scope="{ hover }"
                >
                  <v-expand-transition>
                    <div
                      v-if="hover"
                      @click="previewLogo()"
                      class="d-flex transition-fast-in-fast-out darken-2 v-card--reveal display-3 white--text"
                      style="max-width:104px!important; height:104px!important; background: var(--primary)"
                    >
                      <div style=" text-align:center;">
                        <h4>Vista Previa</h4>
                        <v-icon>zoom_in</v-icon>
                      </div>
                    </div>
                  </v-expand-transition>
                </v-img>
              </v-hover>
              <input
                type="file"
                style="display: none"
                ref="image"
                accept="image/*"
                @change="onFilePicked"
              >
            </v-layout>
          </v-flex>
        </v-layout>
      </v-flex>
    </v-layout>
    <v-flex text-xs-left text-md-left pl-1 xs12>
      <span
        style="font-weight:bold!important"
        class="requeridTitles"
      >* {{$t('message.requiredFields')}}</span>
    </v-flex>
    <!-- Dont remove please
      <v-flex xs12 pt-5 v-if="hasImage===1">
        <span class="grey--text">
          <strong>{{$t('message.vBusinessStepFinalNote')}}</strong>
        </span>
    </v-flex>-->
    <v-flex mt-5>
      <v-btn
        :color="currentBussinesClub.theme.primary"
        dark
        @click="finishSaveBusiness()"
      >{{$t('message.vBusinessStepBusinessFinish')}}</v-btn>
      <v-btn
        flat
        @click="goBack(4)"
        :color="currentBussinesClub.theme.secondary"
      >{{$t('message.vBusinessStepBusinessBack')}}</v-btn>
    </v-flex>
    <v-dialog
      v-model="modalPreview"
      :fullscreen="$vuetify.breakpoint.xsOnly"
      :content-class="$vuetify.breakpoint.xsOnly?'':'dialog-dims'"
      persistent
    >
      <v-card>
        <v-card-actions class="dialog-title linear-gradient" card dark>
          <v-card-title style="padding-left: 0!important;margin-left: 24px!important;">Vista Previa</v-card-title>
          <v-spacer></v-spacer>
          <v-btn
            fab
            flat
            icon
            dark
            small
            style="margin-right: 24px!important;"
            @click="closeModalPreview()"
          >
            <v-icon>close</v-icon>
          </v-btn>
        </v-card-actions>
        <vue-perfect-scrollbar
          class="scroll-area"
          :class="{'dialog-scroll':!$vuetify.breakpoint.xsOnly, 'dialog-full-scroll':$vuetify.breakpoint.xsOnly}"
          :settings="settings"
        >
          <v-layout style="weight:100%!important;height:100%important">
            <v-flex xs12 class="text-xs-center text-sm-center text-md-center text-lg-center">
              <img
                :src="imageUrl"
                style="background-repeat: no-repeat;
    background-size: cover; width:100%"
                v-if="imageUrl"
              >
            </v-flex>
          </v-layout>
        </vue-perfect-scrollbar>
      </v-card>
    </v-dialog>
  </v-form>
  </div>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import { EventBus } from "../../eventBus.js";
import { Chrome } from "vue-color";
import appConfig from "Constants/AppConfig";
import confirmDialog from "Components/ySocialHome/confirmDialog";
export default {
  components: {
    "chrome-picker": Chrome,
    "v-confirm-dialog": confirmDialog
  },
  props: {
    currentBusinessId: String,
    stepData: Object
  },
  data() {
    return {
      FMS_URL: appConfig.FMS_URL,
      wantStore: null,
      valid: false,
      nameRules: [],
      required: "",
      settings: {
        maxScrollbarLength: 160
      },
      modalPreview: false,
      imageName: "",
      imageUrl: "",
      imageFile: "",
      showRequestWebSite: false,
      showNavigationFields: false,
      showImage: false,
      showAskForImage: false,
      /* Data use for primary & secondary color pickers */
      primaryColors: {
        hex: "#000000"
      },
      secondaryColors: {
        hex: "#000000"
      },
      displayPrimaryColorPicker: false,
      displaySecondaryColorPicker: false,
      /*Data use as model for storage on businessCreation state */
      primaryColorValue: "",
      secondaryColorValue: "",
      hasWebSite: null,
      navigationId: "",
      requestWebSiteCreation: null,
      hasImage: null,
      //requestDesignerContact: -1, Disabled until process is defined
      preview: ""
    };
  },
  computed: {
    ...mapGetters([
      "userProfile",
      "currentBussinesClub",
      "owningAnswers",
      "confirmationAnswers"
    ])
  },
  watch: {
    primaryColorValue(val) {
      if (val) {
        this.updateColors(val, "primaryColors");
      }
    },
    secondaryColorValue(val) {
      if (val) {
        this.updateColors(val, "secondaryColors");
      }
    },
    hasWebSite(val) {
      this.requestWebSiteCreation = null;
      this.preview = "";
      this.navigationId = "";
      this.hasImage = null;
      switch (val) {
        /* User has web site */
        case 1:
          this.showNavigationFields = true;
          this.showRequestWebSite = false;
          this.showImage = false;
          this.showAskForImage = true;
          this.getImageInfo();
          break;
        /* User do not has web site */
        case 2:
          this.showNavigationFields = false;
          this.showRequestWebSite = true;
          this.showImage = false;
          this.showAskForImage = false;
          if (this.stepData.businessImage.requestWebSiteCreation) {
            this.requestWebSiteCreation = this.stepData.businessImage.requestWebSiteCreation;
          }
          break;
        /* User do not need web site */
        case 3:
          this.showNavigationFields = false;
          this.showRequestWebSite = false;
          this.showImage = false;
          this.showAskForImage = false;
          break;
      }
    },
    requestWebSiteCreation(val) {
      this.preview = "";
      this.navigationId = "";
      this.hasImage = null;
      switch (val) {
        /* User want web site */
        case 1:
          this.showNavigationFields = true;
          this.showImage = false;
          this.showAskForImage = true;
          this.getImageInfo();
          break;
        /* User do not want web site */
        case 2:
          this.showNavigationFields = false;
          this.showImage = false;
          this.showAskForImage = false;
          break;
      }
    },
    hasImage(val) {
      switch (val) {
        /* User has image */
        case 1:
          this.showImage = true;
          if (this.stepData.businessImage.primaryColor) {
            this.primaryColorValue = this.stepData.businessImage.primaryColor;
          }
          if (this.stepData.businessImage.secondaryColor) {
            this.secondaryColorValue = this.stepData.businessImage.secondaryColor;
          }
          break;
        /* User do not has image and do not need it */
        case 2:
        case 3:
          this.showImage = false;
          break;
      }
    }
  },
  created() {
    EventBus.$on("clearWizard", () => {
      //this.clearData()
    });
    this.required = this.$t("message.required");
    this.nameRules = [v => !!v || this.required];
    /* Load catalogs for this view*/
    Vue.nextTick(() => {
      //this.loadingCountries=true;
      this.$store.dispatch("doGetAnswersOwning").finally(() => {
        //   this.loadingCountries=false;
      });
      this.$store.dispatch("doGetAnswersConfirmation").finally(() => {
        //   this.loadingCountries=false;
      });
    });
  },
  mounted() {
    this.reloadFields();
  },
  methods: {
    getImageInfo() {
      if (this.stepData.url) {
        this.preview = this.stepData.url;
      }
      if (this.stepData.subDomain) {
        this.navigationId = this.stepData.subdomain;
      }
      if (this.stepData.businessImage.hasImage) {
        this.hasImage = this.stepData.businessImage.hasImage;
      }
      if (this.stepData.businessImage.hasImage == 1) {
        if (this.stepData.businessImage.primaryColor) {
          this.primaryColorValue = this.stepData.businessImage.primaryColor;
        }
        if (this.stepData.businessImage.secondaryColor) {
          this.secondaryColorValue = this.stepData.businessImage.secondaryColor;
        }
      }
    },
    reloadFields() {
      this.hasWebSite = Vue.getObjectPath(
        this.stepData,
        "businessImage.hasWebSite",
        null
      );
      this.requestWebSiteCreation = Vue.getObjectPath(
        this.stepData,
        "businessImage.requestWebSiteCreation",
        null
      );
      this.navigationId = Vue.getObjectPath(this.stepData, "subdomain", null);
      this.preview = Vue.getObjectPath(this.stepData, "url", null);
      this.hasImage = Vue.getObjectPath(
        this.stepData,
        "businessImage.hasImage",
        null
      );
      this.primaryColorValue = Vue.getObjectPath(
        this.stepData,
        "businessImage.primaryColor",
        null
      );
      this.secondaryColorValue = Vue.getObjectPath(
        this.stepData,
        "businessImage.secondaryColor",
        null
      );
      //this.hasWebSite = Vue.getObjectPath(this.stepData.businessImage,"hasWebSite",-1);
      let logo = Vue.getObjectPath(
        this.stepData,
        "businessImage.logoLarge",
        ""
      );
      this.wantStore = Vue.getObjectPath(
        this.stepData,
        "businessImage.requestStoreCreation",
        null
      );
      if (logo != "") {
        this.imageUrl = this.FMS_URL + "/image/fetch/" + logo;
      }
    },
    previewLogo() {
      this.modalPreview = true;
    },
    closeModalPreview() {
      this.modalPreview = false;
    },
    pickFile() {
      this.$refs.image.click();
    },
    onFilePicked(e) {
      const files = e.target.files;
      if (files[0] !== undefined) {
        this.imageName = files[0].name;
        if (this.imageName.lastIndexOf(".") <= 0) {
          return;
        }
        const fr = new FileReader();
        fr.readAsDataURL(files[0]);
        fr.addEventListener("load", () => {
          this.imageUrl = fr.result;
          this.imageFile = files[0];
        });
      } else {
        this.imageName = "";
        this.imageFile = "";
        this.imageUrl = "";
      }
    },
    setUrlPreview() {
      this.preview = this.navigationId + ".yovirt.com";
    },
    goBack(data) {
      this.$emit("onGoBack", data);
    },
    hi(){
	if(this.displayPrimaryColorPicker==true){
		this.displayPrimaryColorPicker = false;
	}
	if(this.displaySecondaryColorPicker==true){
		this.displaySecondaryColorPicker = false;
	}
    },
    finishSaveBusiness() {
      /* Validate if fields are required */
      if (this.$refs.form.validate()) {
        let data = {
          hasWebSite: this.hasWebSite ? this.hasWebSite : -1,
          requestWebSiteCreation: this.requestWebSiteCreation
            ? this.requestWebSiteCreation
            : -1,        
          hasImage: this.hasImage ? this.hasImage : -1,
          primaryColor: this.primaryColorValue ? this.primaryColorValue : "",
          secondaryColor: this.secondaryColorValue
            ? this.secondaryColorValue
            : "",
          requestStoreCreation: this.wantStore ? this.wantStore : -1,
          //requestDesignerContact: this.requestDesignerContact,
          logoLarge: ""
        };
        let webSiteData={
          subdomain: this.navigationId,
          url: this.preview
        }
        this.$store
          .dispatch("doAddBusinessStore", {
            businessId: this.currentBusinessId
          })
          .catch(error => {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.couldNotAddBusinessStore"),
              type: "error"
            });
          });
         this.$store
            .dispatch("doUpdateUserBusiness", {
              businessId: this.currentBusinessId,
              field: "webSite",
              webSite: webSiteData,
              path: "webSite"
            })
            .catch(error => {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.couldNotAddBusinessWebSite"),
              type: "error"
            });
          });
            
        if (this.hasImage == 1) {
          if (!this.primaryColorValue) {
            return;
          }
          if (!this.secondaryColorValue) {
            return;
          }
          if (this.imageFile == "") {
            return;
          }
          let imageUpload = this.imageFile;
          this.$store
            .dispatch("doAddBusinessImageLogo", imageUpload)
            .then(image => {
              if (
                image.data.data.mimetype == "image/png" ||
                image.data.data.mimetype == "image/jpg" ||
                image.data.data.mimetype == "image/jpeg" ||
                image.data.data.mimetype == "image/gif"
              ) {
                data.logoLarge = image.data.data.path;
                this.$store
                  .dispatch("doUpdateUserBusiness", {
                    businessId: this.currentBusinessId,
                    field: "businessImage",
                    businessImage: data,
                    path: "businessImage"
                  })
                  .then(() => {
                    this.$emit("onContinue");
                  });
              }
            });
        } else {
          delete data["logoLarge"];
          this.$store
            .dispatch("doUpdateUserBusiness", {
              businessId: this.currentBusinessId,
              field: "businessImage",
              businessImage: data,
              path: "businessImage"
            })
            .then(() => {
              this.$emit("onContinue");
            });
        }
      } else {
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.couldNotAddBusinessImage"),
          type: "error"
        });
      }
    },
    updateColors(color, picker) {
      if (color.slice(0, 1) == "#") {
        this[picker] = {
          hex: color
        };
      }
    },
    showPicker(picker) {
      if (picker === 1) {
	document.querySelector('#final').addEventListener('click', this.documentClick)
        this.displayPrimaryColorPicker = true;
      }
      if (picker === 2) {
	document.querySelector('#final').addEventListener('click', this.documentClickSecondary)        
        this.displaySecondaryColorPicker = true;
      }
    },
    hidePicker(picker) {
      if (picker === 1) {
	document.querySelector('#final').removeEventListener('click', this.documentClick)       
        this.displayPrimaryColorPicker = false;
      }
      if (picker === 2) {
 	document.querySelector('#final').removeEventListener('click', this.documentClickSecondary)
        this.displaySecondaryColorPicker = false;
      }
    },
    togglePicker(picker) {
      if (picker === 1) {
        this.displayPrimaryColorPicker
          ? this.hidePicker(1)
          : this.showPicker(1);
      }
      if (picker === 2) {
        this.displaySecondaryColorPicker
          ? this.hidePicker(2)
          : this.showPicker(2);
      }
    },

    updateFromPrimaryPicker(color) {
      this.primaryColors = color;
      if (color.rgba.a == 1) {
        this.primaryColorValue = color.hex;
      } else {
        this.primaryColorValue =
          "rgba(" +
          color.rgba.r +
          ", " +
          color.rgba.g +
          ", " +
          color.rgba.b +
          ", " +
          color.rgba.a +
          ")";
      }
    },
    updateFromSecondaryPicker(color) {
      this.secondaryColors = color;
      if (color.rgba.a == 1) {
        this.secondaryColorValue = color.hex;
      } else {
        this.secondaryColorValue =
          "rgba(" +
          color.rgba.r +
          ", " +
          color.rgba.g +
          ", " +
          color.rgba.b +
          ", " +
          color.rgba.a +
          ")";
      }
    },
    documentClick(e) {
      var el = this.$refs.primaryColorPicker,
        target = e.target;
      if (el !== target && !el.contains(target)) {
        this.hidePicker(1);
      }
    },
    documentClickSecondary(e) {
      var el = this.$refs.secondaryColorPicker,
        target = e.target;
      if (el !== target && !el.contains(target)) {
        this.hidePicker(2);
      }
    }
  }
};
</script>

<style scoped>
:root {
  --colorSecondary: #ffffff;
  --colorPrimary: #ffffff;
  --bottomVar: 0;
}  
.vc-chrome-mobile {
  width: 100% !important;
}

.span-pick-color {
  text-align: left;
  padding-bottom: 5px;
}

.span-pick-color-mobile {
  text-align: left;
  padding-bottom: 5px;
}

.layout-mobile {
  padding-bottom: 20px;
}

.vc-chrome {
  position: absolute;
  z-index: 9;
}

.vc-alpha {
  display: none;
}

.vc-chrome-fields-wrap {
  display: none;
}

.alignmentStep {
  padding: 0px 0px 0px 12px !important;
}

.current-color {
  display: inline-block;
  width: 16px;
  height: 16px;

  cursor: pointer;
}

.input-group-addon {
  padding: 6px 12px;
  line-height: 1;
  text-align: center;
  border: 1px solid #ccc;
}

.form-control {
  padding: 6px 12px;
  line-height: 1;
  border: 1px solid #ccc;
  width: 48%;
  color: #000;
}

.v-card--reveal {
  align-items: center;
  bottom: 0;
  justify-content: center;
  opacity: 0.5;
  position: absolute;
  width: 100%;
}
</style>
