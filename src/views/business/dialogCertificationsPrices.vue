<template>
  <v-flex xs12 md12 class="profile-flex-padding">
    <v-dialog
      v-model="certificationsPriceDialog"
      :fullscreen="$vuetify.breakpoint.xsOnly"
      max-width="700"
      persistent
    >
      <v-card>
        <v-toolbar class="linear-gradient" dense card dark>
          <v-btn absolute right icon dark @click="closeCertificationsPriceDialog()">
            <v-icon>close</v-icon>
          </v-btn>
          <v-toolbar-title>{{certificationsPriceIndex<0 ?$t('message.addCertificationsPrice'):$t('message.editCertificationsPrice')}}</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items></v-toolbar-items>
        </v-toolbar>
        <v-container>
          <v-form ref="certificationsPrice" onSubmit="return false;" lazy-validation>
            <v-flex xs12 md12>
              <input
                type="file"
                id="userThumb"
                ref="userThumb"
                v-on:change="uploadUserThumb"
                accept="image/x-png, image/gif, image/jpeg"
                style="display:none"
              >
              <div class="post-thumbDialog post-thumb-mobile">
                <v-avatar
                  size="200"
                  @mouseenter="activateEditButton"
                  @mouseleave="cancelEditButton"
                >
                  <v-img
                    class="user-avatar-desktop"
                    :src="getProfileImage(200,256)"
                    lazy-src="/static/img/perfil_yovirt_small.png"
                  >
                    <v-container
                      column
                      fill-height
                      class="animated fadeIn"
                      style="background-color:rgba(0,0,0,0.7); cursor:pointer"
                      v-if="activePencil"
                      @click="updateUserImage"
                    >
                      <v-layout align-center justify-center column>
                        <v-btn icon dark class="animated slideInUp">
                          <v-icon>fas fa-edit fa-lg</v-icon>
                        </v-btn>
                        <h5
                          class="white--text animated slideInUp"
                        >{{certificationsPriceIndex<0 ?$t('message.add')+" "+$t('message.image'):$t('message.edit')+" "+$t('message.image')}}</h5>
                      </v-layout>
                    </v-container>
                  </v-img>
                </v-avatar>
              </div>

              <v-layout row wrap>
                <v-flex style="padding-bottom: 0px;    margin-bottom: -14px;" xs12 md12 lg12 class="profile-flex-padding">
                  <v-text-field
                    :color="currentBussinesClub.theme.primary"
                    v-bind:label="$t('message.title')"
                  ></v-text-field>
                </v-flex>
                <v-flex style="padding-right: 0px;    margin-left: 5px;" xs12 md12 lg12>
                  <v-menu
                    class="pt-0 pb-0"
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
                      style="margin-top: 0px;"
                      class="mr05"
                      clearable
                      readonly
                      ref="date"
                      :color="currentBussinesClub.theme.primary"
                      slot="activator"
                      v-model="date"
                      v-bind:label="$t('message.date')"
                      hide-details
                    ></v-text-field>
                    <v-date-picker
                      :first-day-of-week="1"
                      :locale="selectedLocale.locale"
                      :color="currentBussinesClub.theme.primary"
                      v-model="date"
                      scrollable
                      :max="calculateMax()"
                    >
                      <v-spacer></v-spacer>
                      <v-btn
                        flat
                        :color="currentBussinesClub.theme.secondary"
                        @click="menu = false"
                      >{{$t('message.cancel')}}</v-btn>
                      <v-btn
                        flat
                        :color="currentBussinesClub.theme.primary"
                        @click="$refs.menu.save(date)"
                      >{{$t('message.ok')}}</v-btn>
                    </v-date-picker>
                  </v-menu>
                </v-flex>

                <v-flex xs12 md12 lg12 class="profile-flex-padding">
                  <v-text-field
                    :color="currentBussinesClub.theme.primary"
                    v-bind:label="$t('message.description')"
                  ></v-text-field>
                </v-flex>
              </v-layout>
            </v-flex>

            <v-layout align-end justify-end row fill-height>
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
import AppConfig from "Constants/AppConfig";

export default {
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "coordinatesSelected"
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
      menu: false,
      date: null,
      getObjectPath: Vue.getObjectPath,
      certificationsPriceDialog: false,
      certificationsPriceIndex: 1,
      activePencil: false,
      //Data
      businessName: "",
      businessList: [
        {
          name: "User1"
        },
        {
          name: "User2"
        }
      ]
    };
  },
  methods: {
    updateUserImage() {
      this.$refs.userThumb.click();
    },
    uploadUserThumb: function(event) {
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
      return `${AppConfig.FMS_URL}/image/fetch/${Vue.getObjectPath(
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
    },
    openCertificationsPriceDialog(index) {
      this.certificationsPriceIndex = index;
      this.certificationsPriceDialog = true;
    },
    closeCertificationsPriceDialog() {
      this.certificationsPriceDialog = false;
    },
    closeCertificationsPriceDialog() {
      this.certificationsPriceDialog = false;
    },
    closeDialog() {
      this.certificationsPriceDialog = false;
    },
    activateEditButton() {
      this.activePencil = true;
    },

    cancelEditButton() {
      this.activePencil = false;
    },
    calculateMax() {
      var today = new Date();
      var dd = String(today.getDate()).padStart(2, "0");
      var mm = String(today.getMonth() + 1).padStart(2, "0"); //January is 0!
      var yyyy = today.getFullYear();

      let formattedDate = yyyy + "-" + mm + "-" + dd;

      return formattedDate;
    }
  }
};
</script>
<style>
@media only screen and (max-width: 600px) {
  .post-thumb-mobile {
    text-align: center !important;
    float: unset !important;
  }
}
.post-thumbDialog {
  float: left;
  text-align: left;
}
.post-thumbDialog img {
  display: block;
}
.post-content {
  margin-left: 210px;
}
.borderCards {
  border-style: solid;
  border-width: 1px;
  border-color: #cfd8dc;
}
</style>
