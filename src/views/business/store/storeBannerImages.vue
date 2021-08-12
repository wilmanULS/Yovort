<template>
  <v-card
    class="borderCards"
    :style="'height: 200px; background-image:url(\''+FMS_URL+'/image/fetch/'+item.url+'?tx=resize(h:300)\')!important;background-size:cover!important;'"
  >
    <div style="height:100%!important;width:100%!important;">
      <v-container class="no-padding">
        <v-flex xs12>
          <v-layout align-end justify-start column>
            <v-speed-dial
              v-model="fab"
              direction="bottom"
              open-on-hover
              transition="slide-y-reverse-transition"
            >
              <template v-slot:activator style="height: 100%!important">
                <v-btn v-model="fab" :color="currentBussinesClub.theme.secondary" dark fab small>
                  <v-icon>mdi mdi-dots-vertical</v-icon>
                  <v-icon>mdi mdi-close</v-icon>
                </v-btn>
              </template>
              <v-tooltip left>
                <v-btn
                  fab
                  dark
                  small
                  :color="currentBussinesClub.theme.primary"
                  slot="activator"
                  @click="openBannerImageDialog()"
                >
                  <v-icon>mdi mdi-pencil</v-icon>
                </v-btn>
                {{$t('message.vStoreBannerEdit')}}
              </v-tooltip>
              <v-tooltip left>
                <v-btn
                  fab
                  dark
                  small
                  :color="currentBussinesClub.theme.secondary"
                  slot="activator"
                  @click="openDeleteDialog()"
                >
                  <v-icon>mdi mdi-trash-can</v-icon>
                </v-btn>
                {{$t('message.vStoreBannerDelete')}}
              </v-tooltip>
              <v-tooltip v-if="item.link" left allow-overflow>
                <v-btn
                  id="userbtn"
                  fab
                  dark
                  small
                  :color="currentBussinesClub.theme.primary"
                  slot="activator"
                  @click="gotoLink()"
                >
                  <v-icon>mdi mdi-link</v-icon>
                </v-btn>
                {{$t('message.portfolioImageLink')}}
              </v-tooltip>
            </v-speed-dial>
          </v-layout>
        </v-flex>
      </v-container>
      <div v-if="item.name[0].name" class="info-overlay-close">
        <div
          :title="item.title"
          style="color:white!important;"
          class="info-overlay-title"
        >{{item.name[0].name}}</div>
        <div class="ellipsis">
          <div>
            <p
              style="color:white!important;"
              :title="item.name[0].description.shorte"
            >{{item.name[0].description.short}}</p>
          </div>
        </div>
      </div>
    </div>
    <v-confirm-dialog
      v-bind:titleToolbar="$t('message.vStoreBannerDelete')"
      ref="deleteBanner"
      type="question"
      :heading="$t('message.vStoreBannerDelete')"
      v-bind:message="$t('message.vStoreBannerDeleteQuestion')"
      @onConfirm="removeBanner()"
    ></v-confirm-dialog>
    <v-popup-dialog
      ref="bannerInformationImage"
      :model="bannerImageDialog"
      :title="$t('message.vStoreBannerEdit')"
      @onClose="closeBannerImageDialog"
      @onSave="saveBannerInformationImage"
    >
      <v-card v-if="!bannerImage[0]" height="200px" class="cardAdd borderCards">
        <v-layout
          align-center
          justify-center
          column
          fill-height
          @drag="ondrag"
          @dragstart="ondragstart"
          @dragend="ondragend"
          @dragover="ondragover"
          @dragenter="ondragenter"
          @dragleave="ondragleave"
          @drop="ondrop"
        >
          <form role="form" enctype="multipart/form-data" @submit.prevent="onSubmit" fill-height>
            <div class="drop-area">
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
                    <span class="textAddImage">{{$t("message.vStoreBannerUploadImage")}}</span>
                  </v-flex>
                </v-layout>
              </v-container>
            </div>
          </form>
        </v-layout>
      </v-card>
      <v-img
        @drag="ondrag"
        @dragstart="ondragstart"
        @dragend="ondragend"
        @dragover="ondragover"
        @dragenter="ondragenter"
        @dragleave="ondragleave"
        @drop="ondrop"
        aspect-ratio="1.7"
        v-if="bannerImage[0]"
        height="200px"
        :src="FMS_URL+'/image/fetch/'+bannerImage[0]"
      ></v-img>
      <v-text-field
        :color="currentBussinesClub.theme.primary"
        v-bind:label="$t('message.vStoreBannerEditName')"
        v-model="nameBanner"
      ></v-text-field>
      <v-textarea
        :color="currentBussinesClub.theme.primary"
        v-bind:label="$t('message.vStoreBannerEditDescription')"
        v-model="descriptionBanner"
      ></v-textarea>
      <v-text-field
        :color="currentBussinesClub.theme.primary"
        v-bind:label="$t('message.vStoreBannerEditUrlProduct')"
        v-model="urlProductBanner"
      ></v-text-field>
    </v-popup-dialog>
  </v-card>
</template>
<script>
import confirmDialog from "Components/ySocialHome/confirmDialog";
import Vue from "vue";
import { mapGetters } from "vuex";
import AppConfig from "Constants/AppConfig.js";
import FileManagerService from "Services/FileManagerService.js";
export default {
  props: {
    storeId: String,
    item: Object,
    index: Number
  },
  data() {
    return {
      FMS_URL: AppConfig.FMS_URL,
      getObjectPath: Vue.getObjectPath,
      bannerImageDialog: false,
      fab: false,
      profileTooltip: false,
      descriptionBanner: "",
      urlProductBanner: "",
      nameBanner: "",
      bannerImage: [],
      nameRules: [v => !!v || "Campo requerido"]
    };
  },
  components: {
    "v-confirm-dialog": confirmDialog
  },
  computed: {
    ...mapGetters([
      "currentBussinesClub",
      "businessSelected",
      "businessId",
      "getStoreById"
    ])
  },
  components: {
    "v-confirm-dialog": confirmDialog
  },
  mounted() {
    this.reloadFields();
  },
  methods: {
    reloadFields() {
      //console.log(this.item)
      this.bannerImage = [];
      this.bannerImage.push(this.getObjectPath(this.item, "url", ""));
      this.nameBanner = this.getObjectPath(this.item.name[0], "name", "");
      this.descriptionBanner = this.getObjectPath(
        this.item.name[0],
        "description.long",
        ""
      );
      this.urlProductBanner = this.getObjectPath(this.item, "urlProduct", "");
    },
    /**
     * Go to the image link address
     */
    gotoLink(index) {
      if (!this.item || !this.item.link) {
        return;
      }

      window.open(this.item.link, "_blank");
    },

    /**
     * Ask to delete the given image
     */
    openDeleteDialog() {
      this.$refs.deleteBanner.openDialog();
    },

    /**
     * Remove the given portfolio image
     */
    removeBanner() {
      if (!this.item || !this.item._id) {
        this.$refs.deleteBanner.close();
        return;
      }

      let banner = {
        storesId: this.getStoreById.stores[0]._id,
        storeId: this.getStoreById._id,
        bannerId: this.item._id,
        status: 6
      };

      /* Call the portfolio image delete action */
      this.$store
        .dispatch("deleteBanner", banner)
        .then(res => {
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
        })
        .finally(() => {
          this.$refs.deleteBanner.close();
        });
    },

    /**
     * Open the porfolio image dialog
     */
    openBannerImageDialog() {
      this.$refs.bannerInformationImage.doReset();
      this.bannerImageDialog = true;
    },

    /**
     * Close the porfolio image dialog
     */
    closeBannerImageDialog() {
      this.bannerImageDialog = false;
      this.$refs.form.resetValidation();
    },

    /**
     * Save the portfolio information
     */
    saveBannerInformationImage() {
      if (!this.$refs.bannerInformationImage.doValidate()) {
        return;
      }

      let bannnerName = {
        storesId: this.getStoreById.stores[0]._id,
        storeId: this.getStoreById._id,
        bannerId: this.item._id,
        type: "Name",
        storeData: {
          namesId: this.item.name[0]._id,
          name: this.nameBanner,
          description: {
            long: this.descriptionBanner,
            short: "",
            detail: ""
          },
          urlProduct: this.urlProductBanner
        }
      };

      let url = {
        storesId: this.getStoreById.stores[0]._id,
        storeId: this.getStoreById._id,
        bannerId: this.item._id,
        type: "Url",
        storeData: {
          url: this.bannerImage[0]
        }
      };

      this.$store
        .dispatch("updateBanner", bannnerName)
        .then(res => {
          this.$store.dispatch("setBusinessStore", res.data.data);
          this.$store
            .dispatch("updateBanner", url)
            .then(res => {
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
        })
        .finally(() => {
          this.closeBannerImageDialog();
        });
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
      this.bannerImage = [];

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
      FileManagerService.uploadFiles(this.rutas, formData)
        .then(res => {
          /* Get the images information */
          const images = res.data.data.files.map(file => {
            return `${this.rutas}/${file.path}`;
          });
          this.bannerImage.push(images[0]);
        })
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.sessionErrorDefault"),
            type: "error"
          });
        });
    }
  }
};
</script>
<style scoped>
.info-overlay-close {
  background-color: rgba(0, 0, 0, 0.6) !important;
  position: absolute;
  bottom: 0;
  width: 100% !important;
  height: 25px !important;
  overflow: hidden !important;
  border-radius: 0px 0px 20px 20px !important;
}

.info-overlay-close:hover {
  background-color: rgba(0, 0, 0, 0.6) !important;
  position: absolute;
  bottom: 0;
  width: 100% !important;
  height: 75px !important;
}

.info-overlay-title {
  margin: 0 10px !important;
  white-space: nowrap !important;
  overflow: hidden !important;
  text-overflow: ellipsis !important;
  line-height: 25px;
  font-weight: 700;
}

.ellipsis {
  overflow: hidden;
  white-space: normal;
  height: 50px;
  line-height: 25px;
  margin: 0 10px;
}

.ellipsis:before {
  content: "";
  float: left;
  width: 5px;
  height: 50px;
}

.ellipsis > *:first-child {
  float: right;
  width: 100%;
  margin-left: -5px;
}

.ellipsis:after {
  content: "\02026";
  box-sizing: content-box;
  -webkit-box-sizing: content-box;
  -moz-box-sizing: content-box;
  float: right;
  position: relative;
  top: -25px;
  left: 100%;
  width: 3em;
  margin-left: -3em;
  padding-right: 5px;
  text-align: right;
}
</style>
