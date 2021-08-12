<template>
  <v-form data-vv-scope="step4">
    <v-layout row wrap class="mb-1">
      <v-flex xs12 md2 class="alignmentStep">
        <span
          style="text-align: left;padding-bottom: 5px"
          class="titleCard darker-titles"
        >Color Primario</span>
      </v-flex>
      <v-flex xs12 md4 class="alignmentStep">
        <div class="input-group color-picker" ref="primaryColorPicker">
          <span class="input-group-addon color-picker-container">
            <span
              class="current-color"
              :style="'background-color: ' + primaryColorValue"
              @click="togglePicker(1)"
            ></span>
            <chrome-picker
              :value="primaryColors"
              @input="updateFromPrimaryPicker"
              v-if="displayPrimaryColorPicker"
            />
          </span>
        </div>
      </v-flex>

      <v-flex xs12 md2 class="alignmentStep">
        <span
          style="text-align: left;padding-bottom: 5px"
          class="titleCard darker-titles"
        >Color Secundario</span>
      </v-flex>
      <v-flex xs12 md4 class="alignmentStep">
        <div class="input-group color-picker" ref="secondaryColorPicker">
          <span class="input-group-addon color-picker-container">
            <span
              class="current-color"
              :style="'background-color: ' + secondaryColorValue"
              @click="togglePicker(2)"
            ></span>
            <chrome-picker
              :value="secondaryColors"
              @input="updateFromSecondaryPicker"
              v-if="displaySecondaryColorPicker"
            />
          </span>
        </div>
      </v-flex>
    </v-layout>
    <v-layout row wrap>

      <v-flex xs12 md6>
        <v-card
          class="colorBorderCards"
          color="transparent"
          style="heigth: 100%!important;"
          :class="{'mr05': $vuetify.breakpoint.mdAndUp}"
        >
          <v-card-title>
            <span class="titleCard darker-titles">{{$t('message.certifications')}}</span>
            <v-btn
              small
              :color="currentBussinesClub.theme.primary"
              fab
              dark
              absolute
              right
              @click="openCertificationDialog(-1)"
            >
              <v-icon>add</v-icon>
            </v-btn>
          </v-card-title>
          <v-card-text>
            <v-container class="no-padding">
              <v-carousel>
                <v-carousel-item v-for="(item,i) in items" :key="i" :src="item.src"></v-carousel-item>
              </v-carousel>
            </v-container>
          </v-card-text>
        </v-card>
      </v-flex>
      <v-flex xs12 md6>
        <v-card
          class="colorBorderCards"
          color="transparent"
          style="heigth: 100%!important;"
          :class="{'mr05': $vuetify.breakpoint.mdAndUp}"
        >
          <v-card-title>
            <span class="titleCard darker-titles">{{$t('message.certifications')}}</span>
            <v-btn
              small
              :color="currentBussinesClub.theme.primary"
              fab
              dark
              absolute
              right
              @click="openCertificationDialog(-1)"
            >
              <v-icon>add</v-icon>
            </v-btn>
          </v-card-title>
          <v-card-text>
          <v-container grid-list-sm fluid class="no-padding">
            <v-layout row wrap>
              <vue-perfect-scrollbar ref="scrollbar" class="scroll-area">
              <v-flex v-for="n in 18" :key="n" xs4 d-flex>
                <v-card flat tile class="d-flex">
                  <v-img
                    :src="`https://picsum.photos/500/300?image=${n * 5 + 20}`"
                    :lazy-src="`https://picsum.photos/10/6?image=${n * 5 + 20}`"
                    aspect-ratio="1"
                    class="grey lighten-2"
                  >
                    <template v-slot:placeholder>
                      <v-layout fill-height align-center justify-center ma-0>
                        <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
                      </v-layout>
                    </template>
                  </v-img>
                </v-card>
              </v-flex>
              </vue-perfect-scrollbar>
            </v-layout>
              
              
              
              </v-container>
          </v-card-text>
        </v-card>
      </v-flex>
      
    </v-layout>

    
  </v-form>
</template>
<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import AppConfig from "Constants/AppConfig";
import { Photoshop, chrome } from "vue-color";
import { Sketch } from "vue-color";
import { Chrome } from "vue-color";
import VueUploadMultipleImageGallery from "vue-upload-multiple-image";


export default {
  components: {
    "vue-upload-multiple-image-gallery": VueUploadMultipleImageGallery,
    "photoshop-picker": Photoshop,
    "sketch-picker": Sketch,
    "chrome-picker": Chrome
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
    }
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
      "currentUserBusiness"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  data() {
    return {
      items: [
        {
          src: "https://cdn.vuetifyjs.com/images/carousel/squirrel.jpg"
        },
        {
          src: "https://cdn.vuetifyjs.com/images/carousel/sky.jpg"
        },
        {
          src: "https://cdn.vuetifyjs.com/images/carousel/bird.jpg"
        },
        {
          src: "https://cdn.vuetifyjs.com/images/carousel/planet.jpg"
        }
      ],
      /* Data use for primary & secondary color pickers */
      primaryColors: {
        hex: "#000000"
      },
      secondaryColors: {
        hex: "#000000"
      },
      primaryColorValue: "",
      secondaryColorValue: "",
      displayPrimaryColorPicker: false,
      displaySecondaryColorPicker: false,
      /* */
      imagesGallery: [],
      imagesSlider: [],
      primaryColor: {
        hex: ""
      },
      secondColor: {
        hex: ""
      },
      imageUrl: "",
      imageUrlIsoType: "",
      activePencil: false,
      activePencilIsoType: false
    };
  },
  methods: {
    updateColors(color, picker) {
      if (color.slice(0, 1) == "#") {
        this[picker] = {
          hex: color
        };
      }
    },
    showPicker(picker) {
      if (picker === 1) {
        this.displayPrimaryColorPicker = true;
        document.addEventListener("click", this.documentClick);
      }
      if (picker === 2) {
        this.displaySecondaryColorPicker = true;
        document.addEventListener("click", this.documentClickSecondary);
      }
    },
    hidePicker(picker) {
      if (picker === 1) {
        document.removeEventListener("click", this.documentClick);
        this.displayPrimaryColorPicker = false;
      }
      if (picker === 2) {
        document.addEventListener("click", this.documentClickSecondary);
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
    },

    //Methods to upload multiple images
    uploadImageSuccessGallery(formData, index, fileList) {
      // Upload image api
      // axios.post('http://your-url-upload', { data: formData }).then(response => {
      // })
    },
    beforeRemoveGallery(index, done, fileList) {
      var r = confirm("remove image");
      if (r == true) {
        done();
      } else {
      }
    },
    editImageGallery(formData, index, fileList) {},
    dataChangeGallery(data) {},
    //Methods to upload multiple images
    uploadImageSuccessSlider(formData, index, fileList) {
      // Upload image api
      // axios.post('http://your-url-upload', { data: formData }).then(response => {
      // })
    },
    beforeRemoveSlider(index, done, fileList) {
      var r = confirm("remove image");
      if (r == true) {
        done();
      } else {
      }
    },
    editImageSlider(formData, index, fileList) {},
    dataChangeSlider(data) {},
    updatBusinessLogo() {
      this.$refs.logoThumb.click();
    },
    activateEditButton() {
      this.activePencil = true;
    },

    cancelEditButton() {
      this.activePencil = false;
    },
    activateEditButtonIsoType() {
      this.activePencilIsoType = true;
    },

    cancelEditButtonIsoType() {
      this.activePencilIsoType = false;
    },
    uploadBusinessLogo: function(event) {
      let input = event.target;
      if (input.files && input.files[0]) {
        var reader = new FileReader();
        reader.onload = e => {
          /* this.$store.dispatch("updateUserThumb", input.files[0]); */
        };
        reader.readAsDataURL(input.files[0]);
      }
    },
    getProfileImage(realSize, size) {
      this.imageUrl = `${AppConfig.FMS_URL}/image/fetch/${Vue.getObjectPath(
        this.userProfile,
        "personal_info.picture",
        "/static/img/perfil_yovirt.png"
      )}?tx=resize(w:${realSize},h:${realSize})&username=${Vue.getObjectPath(
        this.userProfile,
        "personal_info.names",
        ""
      )} ${Vue.getObjectPath(
        this.userProfile,
        "personal_info.surnames",
        ""
      )}&color=${Vue.getObjectPath(
        this.userProfile,
        "personal_info.color",
        "no"
      )}&size=${size}`;

      return this.imageUrl;
    },
    getIsoTypeImage(realSize, size) {
      this.imageUrlIsoType = `${
        AppConfig.FMS_URL
      }/image/fetch/${Vue.getObjectPath(
        this.userProfile,
        "personal_info.picture",
        "/static/img/perfil_yovirt.png"
      )}?tx=resize(w:${realSize},h:${realSize})&username=${Vue.getObjectPath(
        this.userProfile,
        "personal_info.names",
        ""
      )} ${Vue.getObjectPath(
        this.userProfile,
        "personal_info.surnames",
        ""
      )}&color=${Vue.getObjectPath(
        this.userProfile,
        "personal_info.color",
        "no"
      )}&size=${size}`;

      return this.imageUrlIsoType;
    }
  }
};
</script>
<style>
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
.current-color {
  display: inline-block;
  width: 72px;
  height: 48px;
  background-color: #000;
  cursor: pointer;
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
</style>
