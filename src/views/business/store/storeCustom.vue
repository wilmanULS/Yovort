<template>
  <v-layout row wrap mt-2 v-if="storeDetail!=null">
    <v-flex xs12>
      <span class="vb-cp-title">{{"Personalización de Colores"}}</span>
    </v-flex>
    <v-flex xs12 sm6>
      <span style="padding-top:20px;" class="grey--text">Color Primario (*)</span>
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
            class="picker"
            :value="primaryColors"
            @input="updateFromPrimaryPicker"
            v-show="displayPrimaryColorPicker"
          />
        </span>
      </div>
    </v-flex>
    <v-flex xs12 sm6>
      <span style="padding-top:20px" class="grey--text">Color Secundario (*)</span>
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
            class="picker"
            :value="secondaryColors"
            @input="updateFromSecondaryPicker"
            v-if="displaySecondaryColorPicker"
          />
        </span>
      </div>
    </v-flex>
    <v-flex xs12>
      <v-layout mt-1>
        <v-btn
          class="rounded-buttons"
          dark
          @click="saveColors()"
          :color="currentBussinesClub.theme.primary"
        >{{"Aplicar Colores"}}</v-btn>
      </v-layout>
    </v-flex>
    <v-flex xs12 sm6 mt-1>
      <span class="vb-cp-title">{{"Logotipo de la Tienda"}}</span>
    </v-flex>
    <v-flex xs12 sm6 mt-1 v-if="$vuetify.breakpoint.mdAndUp">
      <span class="vb-cp-title">{{"Isotipo de la Tienda"}}</span>
    </v-flex>

    <v-flex xs12 sm6 mt-1>
      <v-card
        v-if="logotype==''"
        height="200px"
        width="200px"
        class="cardAdd borderCards"
      >
        <v-layout align-center justify-center column fill-height>
          <form role="form" enctype="multipart/form-data" @submit.prevent="onSubmit" fill-height>
            <div
              class="drop-area"
              @drag="ondrag"
              @dragstart="ondragstart"
              @dragend="ondragend"
              @dragover="ondragover"
              @dragenter="ondragenter"
              @dragleave="ondragleave"
              @drop="ondrop"
            >
              <v-container class="no-padding">
                <v-layout row wrap>
                  <v-flex
                    xs12
                    text-xs-center
                    class="center"
                    @dragenter="ondragenter"
                    @dragleave="ondragleave"
                  >
                    <div>
                      <input type="file" ref="files" name="files" @change="ondrop" hidden>
                      <v-btn
                        small
                        :color="currentBussinesClub.theme.primary"
                        fab
                        dark
                        @click="$refs.files.click()"
                      >
                        <v-icon>mdi mdi-upload</v-icon>
                      </v-btn>
                    </div>
                    <span class="vb-cp-title">{{$t("message.vBusinessStepFinalUploadLogo")}}</span>
                  </v-flex>
                </v-layout>
              </v-container>
            </div>
          </form>
        </v-layout>
      </v-card>
     
      <v-flex
      v-if="logotype!=''"
        xs12
        mt-1
        style="height:100%!important"
        class="cardHover drop-area"
        @drag="ondrag"
        @dragstart="ondragstart"
        @dragend="ondragend"
        @dragover="ondragover"
        @dragenter="ondragenter"
        @dragleave="ondragleave"
        @drop="ondrop"
      >
        <v-hover>
          <v-img
            :src="FMS_URL+'/image/fetch/'+logotype+'?tx=resize(h:300)'"
            style="border-radius: 20px;border: 1px solid var(--primary); width:200px !important; height:200px!important"
            v-if="isotype!=null"
            slot-scope="{ hover }"
            @drop="ondrop"
          >
            <v-expand-transition>
              <div
                v-if="hover"
                @click="PreviewLogo(0)"
                class="d-flex transition-fast-in-fast-out darken-2 v-card--reveal display-3 white--text"
                style="width:200px !important; height:200px!important; background: var(--primary)"
              >
                <div style=" text-align:center;">
                  <h4>Vista Previa</h4>
                  <v-icon>zoom_in</v-icon>
                </div>
              </div>
            </v-expand-transition>
          </v-img>
        </v-hover>
      </v-flex>
    </v-flex>
 <v-flex xs12 mt-1 v-if="$vuetify.breakpoint.mdAndDown">
      <span class="vb-cp-title">{{"Isotipo de la Tienda"}}</span>
    </v-flex>
    <v-flex xs12 sm6 mt-1>
      <v-card
        v-if="isotype==''"
        height="200px"
        width="200px"
        class="cardAdd borderCards"
      >
        <v-layout align-center justify-center column fill-height>
          <form role="form" enctype="multipart/form-data" @submit.prevent="onSubmit" fill-height>
            <div
              class="drop-area"
              @drag="ondrag"
              @dragstart="ondragstart"
              @dragend="ondragend"
              @dragover="ondragover"
              @dragenter="ondragenter"
              @dragleave="ondragleave"
              @drop="ondropIso"
            >
              <v-container class="no-padding">
                <v-layout row wrap>
                  <v-flex
                    xs12
                    text-xs-center
                    class="center"
                    @dragenter="ondragenter"
                    @dragleave="ondragleave"
                  >
                    <div>
                      <input
                        type="file"
                        ref="filesIso"
                        name="filesIso"
                        @change="ondropIso"
                        multiple
                        hidden
                      >
                      <v-btn
                        small
                        :color="currentBussinesClub.theme.primary"
                        fab
                        dark
                        @click="$refs.filesIso.click()"
                      >
                        <v-icon>mdi mdi-upload</v-icon>
                      </v-btn>
                    </div>
                    <span class="vb-cp-title">{{$t("message.vStoreCustomUploadIsotipo")}}</span>
                  </v-flex>
                </v-layout>
              </v-container>
            </div>
          </form>
        </v-layout>
      </v-card>
      <v-flex
      v-if="isotype!=''"
        xs12
        mt-1
        style="height:100%!important"
        class="drop-area"
        @drag="ondrag"
        @dragstart="ondragstart"
        @dragend="ondragend"
        @dragover="ondragover"
        @dragenter="ondragenter"
        @dragleave="ondragleave"
        @drop="ondropIso"
      >
        <v-hover>
          <v-img
            :src="FMS_URL+'/image/fetch/'+isotype+'?tx=resize(h:300)'"
            style=" border-radius: 20px;border: 1px solid var(--primary); width:200px !important; height:200px!important"
            v-if="isotype!=''"
            slot-scope="{ hover }"
            @drop="ondropIso"
          >
            <v-expand-transition>
              <div
                v-if="hover"
                @click="PreviewLogo(1)"
                class="d-flex transition-fast-in-fast-out darken-2 v-card--reveal display-3 white--text"
                style="width:200px !important; height:200px!important; background: var(--primary)"
              >
                <div style=" text-align:center;">
                  <h4>Vista Previa</h4>
                  <v-icon>zoom_in</v-icon>
                </div>
              </div>
            </v-expand-transition>
          </v-img>
        </v-hover>
      </v-flex>
    </v-flex>
    <v-dialog
      v-model="modalPreviewIso"
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
            @click="CloseModalPreview(1)"
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
              <v-img
                :src="FMS_URL+'/image/fetch/'+isotype+'?tx=resize(h:150)'"
                style="background-repeat: no-repeat;
  background-size: cover; width:100%"
                v-if="isotype!=''"
              ></v-img>
            </v-flex>
          </v-layout>
        </vue-perfect-scrollbar>
      </v-card>
    </v-dialog>
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
            @click="CloseModalPreview(0)"
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
              <v-img
                :src="FMS_URL+'/image/fetch/'+logotype+'?tx=resize(h:150)'"
                style="background-repeat: no-repeat;
  background-size: cover; width:100%"
                v-if="logotype!=''"
              ></v-img>
            </v-flex>
          </v-layout>
        </vue-perfect-scrollbar>
      </v-card>
    </v-dialog>
  </v-layout>
</template>

<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import AppConfig from "Constants/AppConfig.js";
import FileManagerService from "Services/FileManagerService.js";
import { Chrome } from "vue-color";
export default {
  components: {
    "chrome-picker": Chrome
  },
  computed: {
    ...mapGetters([
      "currentBussinesClub",
      "businessSelected",
      "businessId",
      "getStoreById"
    ])
  },
  data() {
    return {
      storePattern: null,
      storeDetail: null,
      logotype: null,
      isotype: null,
      settings: {
        maxScrollbarLength: 160
      },
      rutas: "logoIsoStore",
      FMS_URL: AppConfig.FMS_URL,
      showImage: false,
      modalPreview: false,
      modalPreviewIso: false,
      logo: [{}, {}],
      imageName: "",
      imageUrl: "",
      imageUrlIso: "",
      imageFile: "",
      imageNameIso: "",
      imageUrlIso: "",
      imageFileIso: "",
      displayPrimaryColorPicker: false,
      displaySecondaryColorPicker: false,
      primaryColorValue: "",
      secondaryColorValue: "",
      getObjectPath: Vue.getObjectPath,
      secondaryColors: {
        hex: "#000000"
      },
      primaryColors: {
        hex: "#000000"
      }
    };
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
  mounted() {
    this.$nextTick(() => {
      this.storePattern = this.getStoreById;
      this.storeDetail = this.getStoreById.stores[0];
      //guarda los colores si los creo en el business
      if(this.getObjectPath(this.businessSelected,"finalDetails.businessImage.primaryColor",null)&&this.getObjectPath(this.businessSelected,"finalDetails.businessImage.secondaryColor",null)&&this.getObjectPath(this.storeDetail.customize,"colors.primary","")==""&&this.getObjectPath(this.storeDetail.customize,"colors.secondary","")==""){
   
        this.primaryColorValue =this.getObjectPath(this.businessSelected,"finalDetails.businessImage.primaryColor","");
        this.secondaryColorValue = this.getObjectPath(this.businessSelected,"finalDetails.businessImage.secondaryColor","")
        this.saveColorsInit()
      }else{
     
        this.primaryColorValue = this.getObjectPath(this.storeDetail.customize,"colors.primary","");
        this.secondaryColorValue = this.getObjectPath(this.storeDetail.customize,"colors.secondary","");
      }
      //guarda logo de la tienda si lo creo en el business
      if(this.getObjectPath(this.businessSelected,"finalDetails.businessImage.logoLarge",null)&&this.getObjectPath(this.storeDetail,"customize.logotype","")==""){
      this.logotype=this.getObjectPath(this.businessSelected,"finalDetails.businessImage.logoLarge","") ;
      this.isotype = this.getObjectPath(this.storeDetail,"customize.isotype","");
      this.$store
            .dispatch("updateIsoLogoType",{
              logotype: `${this.logotype}`,
              storeId: this.storeDetail._id
            })
            .then(res => {
              this.storePattern = res.data.data;
              this.storeDetail = res.data.data.stores[0];
              this.primaryColorValue = this.storeDetail.customize.colors.primary;
              this.secondaryColorValue = this.storeDetail.customize.colors.secondary;
              this.logotype = this.storeDetail.customize.logotype;
              this.isotype = this.storeDetail.customize.isotype;
              this.$store.dispatch("setBusinessStore", res.data.data);
            })
            .catch(() => {
              this.$store.dispatch("doNotification", {
                msg: this.$t("message.sessionErrorDefault"),
                type: "error"
              });
            });
      }else{
        this.logotype = this.getObjectPath(this.storeDetail,"customize.logotype","");
        this.isotype = this.getObjectPath(this.storeDetail,"customize.isotype","");
      }
      console.log(this.businessSelected)
    });
  },
  methods: {
    PreviewLogo(type) {
      switch (type) {
        case 0:
          this.modalPreview = true;
          break;
        case 1:
          this.modalPreviewIso = true;
          break;
        default:
          break;
      }
    },
    CloseModalPreview(type) {
      switch (type) {
        case 0:
          this.modalPreview = false;
          break;
        case 1:
          this.modalPreviewIso = false;
          break;
        default:
          break;
      }
    },
    pickFile() {
      this.$refs.image.click();
    },
    pickFileIso() {
      this.$refs.imageIso.click();
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
    updateColors(color, picker) {
      if (color.slice(0, 1) == "#") {
        this[picker] = {
          hex: color
        };
      }
    },
    showPicker(picker) {
      if (picker === 1) {
        document.addEventListener("click", this.documentClick);
        this.displayPrimaryColorPicker = true;
      }
      if (picker === 2) {
        document.addEventListener("click", this.documentClickSecondary);
        this.displaySecondaryColorPicker = true;
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

    onFilePickedIso(e) {
      const files = e.target.files;
      if (files[0] !== undefined) {
        this.imageNameIso = files[0].name;
        if (this.imageNameIso.lastIndexOf(".") <= 0) {
          return;
        }
        const fr = new FileReader();
        fr.readAsDataURL(files[0]);
        fr.addEventListener("load", () => {
          this.imageUrlIso = fr.result;
          this.imageFileIso = files[0];
        });
      } else {
        this.imageNameIso = "";
        this.imageFileIso = "";
        this.imageUrlIso = "";
      }
    },
    ondrag(event) {
      event.preventDefault();
      event.stopPropagation();
    },
    ondragstart(event) {
      event.preventDefault();
      event.stopPropagation();
    },
    ondragend(event) {
      event.preventDefault();
      event.stopPropagation();
    },
    ondragover(event) {
      event.preventDefault();
      event.stopPropagation();
    },
    ondragenter(event) {
      event.preventDefault();
      event.stopPropagation();
      this.dragging = true;
    },
    ondragleave(event) {
      event.preventDefault();
      event.stopPropagation();
      this.dragging = false;
    },
    ondrop(event) {
      event.preventDefault();
      event.stopPropagation();
      this.logo = [];

      /* Get the selected files */
      let files = event.target.files || event.dataTransfer.files;

      /* Initialize the form data */
      let formData = new FormData();

      /* Iteate all files to check images */
      let images = [];
      let cnt = 0;
      for (let i = 0; i < files.length; i++) {
        /* Check if the file is a valid image by extension */
        let file = files[i];
        if (/\.(jpe?g|png|gif|bmp)$/i.test(file.name) && cnt < 20) {
          cnt++;
          formData.append("files", file, file.name);
        }
      }
      /* Upload the images to the file manager service */
      var imageupdaload = FileManagerService.uploadFiles("", formData)
        .then(res => {
          var result = res.data.data.files.map(file => {
            return {
              logotype: `${file.path}`,
              storeId: this.storeDetail._id
            };
          });

          this.$store
            .dispatch("updateIsoLogoType", result[0])
            .then(res => {
              this.storePattern = res.data.data;
              this.storeDetail = res.data.data.stores[0];
              this.primaryColorValue = this.storeDetail.customize.colors.primary;
              this.secondaryColorValue = this.storeDetail.customize.colors.secondary;
              this.logotype = this.storeDetail.customize.logotype;
              this.isotype = this.storeDetail.customize.isotype;
              this.$store.dispatch("setBusinessStore", res.data.data);

              this.$store.dispatch("doNotification", {
                msg: this.$t("message.infoUpdate"),
                type: "success"
              });
            })
            .catch(() => {
              this.$store.dispatch("doNotification", {
                msg: this.$t("message.sessionErrorDefault"),
                type: "error"
              });
            });
        })
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.sessionErrorDefault"),
            type: "error"
          });
        });
    },
    ondropIso(event) {
      event.preventDefault();
      event.stopPropagation();
      this.logo = [];

      /* Get the selected files */
      let files = event.target.files || event.dataTransfer.files;
      /* Initialize the form data */
      let formData = new FormData();

      /* Iteate all files to check images */
      let images = [];
      let cnt = 0;
      for (let i = 0; i < files.length; i++) {
        /* Check if the file is a valid image by extension */
        let file = files[i];
        if (/\.(jpe?g|png|gif|bmp)$/i.test(file.name) && cnt < 20) {
          cnt++;
          formData.append("files", file, file.name);
        }
      }
      /* Upload the images to the file manager service */
      var imageupdaload = FileManagerService.uploadFiles("", formData)
        .then(res => {
          var result = res.data.data.files.map(file => {
            return {
              isotype: `${file.path}`,
              storeId: this.storeDetail._id
            };
          });

          this.$store
            .dispatch("updateIsoLogoType", result[0])
            .then(res => {
              this.storePattern = res.data.data;
              this.storeDetail = res.data.data.stores[0];
              this.primaryColorValue = this.storeDetail.customize.colors.primary;
              this.secondaryColorValue = this.storeDetail.customize.colors.secondary;
              this.logotype = this.storeDetail.customize.logotype;
              this.isotype = this.storeDetail.customize.isotype;
               this.$store.dispatch("setBusinessStore", res.data.data);
              this.$store.dispatch("doNotification", {
                msg: this.$t("message.infoUpdate"),
                type: "success"
              });
            })
            .catch(() => {
              this.$store.dispatch("doNotification", {
                msg: this.$t("message.sessionErrorDefault"),
                type: "error"
              });
            });
        })
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.sessionErrorDefault"),
            type: "error"
          });
        });
    },
    saveColorsInit() {
      let colors = {
        colors: {
          primary: this.primaryColorValue,
          secondary: this.secondaryColorValue
        },
        storeId: this.storeDetail._id
      };
      this.$store.dispatch("updateStoreColors", colors).then((res) => {
      this.$store.dispatch("setBusinessStore", res.data.data);
      });
    },
    saveColors() {
      let colors = {
        colors: {
          primary: this.primaryColorValue,
          secondary: this.secondaryColorValue
        },
        storeId: this.storeDetail._id
      };
      this.$store.dispatch("updateStoreColors", colors).then((res) => {
      this.$store.dispatch("setBusinessStore", res.data.data);
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.infoUpdate"),
          type: "success"
        });
      });
    }
  }
};
</script>

<style scoped>
.cardAdd {
  border: dashed 2px gray !important;
}
.current-color {
  display: inline-block;
  width: 16px;
  height: 16px;

  cursor: pointer;
}
.form-control {
  padding: 6px 12px;
  line-height: 1;
  border: 1px solid #ccc;
  width: 48%;
  color: #000;
}
.input-group-addon {
  padding: 6px 12px;
  line-height: 1;
  text-align: center;
  border: 1px solid #ccc;
}
.picker {
  position: absolute !important;
  z-index: 1 !important;
}
</style>
