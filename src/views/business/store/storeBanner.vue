<template>
  <v-layout row wrap mt-2 v-if="storeDetail!=null">
    <v-flex xs12 sm4 md4 lg4 pa-2>
      <!-- 		<v-card height="200px" class="cardAdd borderCards">
			<v-layout align-center justify-center column fill-height @drag="ondrag" @dragstart="ondragstart" @dragend="ondragend" @dragover="ondragover" @dragenter="ondragenter" @dragleave="ondragleave" @drop="ondrop">
				<form role="form" enctype="multipart/form-data" @submit.prevent="onSubmit" fill-height>
					<div class="drop-area">
						<v-container class="no-padding">
							<v-layout row wrap>
								<v-flex xs12 text-xs-center class="center" @dragenter="ondragenter" @dragleave="ondragleave">
									<div>
										<input type="file" ref="files" name="files" @change="ondrop" hidden>
										<v-btn small :color="currentBussinesClub.theme.primary" fab dark @click="$refs.files.click()">
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
      </v-card>-->
      <v-card height="200px" class="cardAdd borderCards">
        <v-layout align-center justify-center column fill-height>
          <div class="drop-area">
            <v-container class="no-padding">
              <v-layout row wrap>
                <v-flex xs12 text-xs-center class="center">
                  <v-btn
                    small
                    :color="currentBussinesClub.theme.primary"
                    fab
                    dark
                    @click="openDialogBanner"
                  >
                    <v-icon>mdi mdi-plus</v-icon>
                  </v-btn>
                </v-flex>
                <v-flex xs12 style="margin-top:65px">
                  <span class="infoCardText">{{$t("message.vStoreBannerAdd")}}</span>
                </v-flex>
              </v-layout>
            </v-container>
          </div>
        </v-layout>
      </v-card>
    </v-flex>
    <v-flex
      xs12
      sm4
      md4
      lg4
      v-for="(item, index) in getStoreById.stores[0].customize.banners"
			v-if="item.status==1"
      :key="index"
      pa-2
    >
      
        <v-banner-images :item="item"></v-banner-images>
   
    </v-flex>

    <v-popup-dialog
      ref="addBannerDialog"
      :model="dialogBanner"
      :title="$t('message.vStoreBannerEdit')"
      @onClose="closeBannerDialog"
      @onSave="saveBanner"
    >
      <v-form ref="form" v-model="valid" lazy-validation>
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
          :rules="nameRules"
        ></v-text-field>
        <v-textarea
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.vStoreBannerEditDescription')"
          v-model="descriptionBanner"
          :rules="nameRules"
        ></v-textarea>
        <v-text-field
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.vStoreBannerEditUrlProduct')"
          v-model="urlProductBanner"
          :rules="nameRules"
        ></v-text-field>
      </v-form>
    </v-popup-dialog>
  </v-layout>
</template>

<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import AppConfig from "Constants/AppConfig.js";
import FileManagerService from "Services/FileManagerService.js";
import BannerImages from "./storeBannerImages";
export default {
  data() {
    return {
      valid: true,
      FMS_URL: AppConfig.FMS_URL,
      storePattern: null,
      storeDetail: null,
      banners: null,
      dragging: false,
      rutas: "bannerStore",
      dialogBanner: false,
      nameBanner: "",
      urlProductBanner: "",
      descriptionBanner: "",
      bannerImage: [],
      nameRules: [v => !!v || "Campo requerido"]
    };
  },
  components: {
    "v-banner-images": BannerImages
  },
  computed: {
    ...mapGetters([
      "currentBussinesClub",
      "businessSelected",
      "businessId",
      "getStoreById"
    ])
  },
  mounted() {
    this.$nextTick(() => {
      this.storePattern = this.getStoreById;
      this.storeDetail = this.getStoreById.stores[0];
      this.banners = this.getStoreById.stores[0].customize.banners;
    });
  },
  methods: {
    openDialogBanner() {
      this.dialogBanner = true;
    },
    closeBannerDialog() {
      this.nameBanner = "";
      this.urlProductBanner = "";
      this.descriptionBanner = "";
      this.dialogBanner = false;
      this.bannerImage = [];
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
            return `${this.rutas}/${
              file.path
            }`; /* {
              banners: {
                name: [
                  {
                    description: {
                      long: "description long",
                      short: "description short",
                      detail: "description detail"
                    },
                    name: "Name Banner",
                    lang: 7
                  }
                ],
                url: `${this.rutas}/${file.path}`,
                productId: "5ce5f75be0274d2bb8cd573d",
                status: 1
              },
              storeId: this.storeDetail._id
            }; */
          });
          this.bannerImage.push(images[0]);

          /*  this.$store
            .dispatch("createBanners", images[0])
            .then(res => {
              this.$store.dispatch("doNotification", {
                msg: this.$t("message.infoUpdate"),
                type: "success"
              });
              this.storePattern = res.data.data;
              this.storeDetail = res.data.data.stores[0];
              this.banners = res.data.data.stores[0].customize.banners;
              this.$store.dispatch("setBusinessStore", res.data.data);
            })
            .catch(error => {
              this.$store.dispatch("doNotification", {
                msg: this.$t("message.sessionErrorDefault"),
                type: "error"
              });
            }); */
        })
        .catch(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.sessionErrorDefault"),
            type: "error"
          });
        });
    },
    saveBanner() {
      if (this.$refs.form.validate()) {
        let newBanner = {
          banners: {
            name: [
              {
                description: {
                  long: this.descriptionBanner,
                  short: "",
                  detail: ""
                },
                name: this.nameBanner,
                lang: 7
              }
            ],
            url: this.bannerImage[0],
            productId: "5ce5f75be0274d2bb8cd573d",
            urlProduct: this.urlProductBanner,
            status: 1
          },
          storeId: this.storeDetail._id
        };

        this.$store
          .dispatch("createBanners", newBanner)
          .then(res => {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.infoUpdate"),
              type: "success"
            });
            this.storePattern = res.data.data;
            this.storeDetail = res.data.data.stores[0];
            this.banners = res.data.data.stores[0].customize.banners;
            this.$store.dispatch("setBusinessStore", res.data.data);
          })
          .catch(error => {
            this.$store.dispatch("doNotification", {
              msg: this.$t("message.sessionErrorDefault"),
              type: "error"
            });
          })
          .finally(() => {
            this.closeBannerDialog();
          });
        console.log(newBanner);
      }
    }
  }
};
</script>

<style scoped>
.cardAdd {
  border: dashed 2px gray !important;
}
</style>
