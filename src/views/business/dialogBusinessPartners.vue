<template>
  <v-flex xs12 md12>
    <v-dialog
      v-model="businessPartnerDialog"
      :fullscreen="$vuetify.breakpoint.xsOnly"
      max-width="700"
      persistent
    >
      <v-card style="height: 300px!important">
        <v-toolbar class="linear-gradient" dense card dark>
          <v-btn absolute right icon dark @click="closeBusinessPartnerDialog()">
            <v-icon>close</v-icon>
          </v-btn>
          <v-toolbar-title>{{businessPartnerIndex<0 ?$t('message.addBusinessPartner'):$t('message.editBusinessPartner')}}</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items></v-toolbar-items>
        </v-toolbar>
        <v-container>
          <v-form ref="businessPartner" onSubmit="return false;" lazy-validation>
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
                        >{{businessPartnerIndex<0 ?$t('message.add')+" "+$t('message.logo'):$t('message.edit')+" "+$t('message.logo')}}</h5>
                      </v-layout>
                    </v-container>
                  </v-img>
                </v-avatar>
              </div>

              <v-layout row wrap>
                <v-flex xs12 md12 lg12 class="profile-flex-padding">
                  <v-autocomplete
                    ref="business"
                    :no-data-text="$t('message.nodatafound')"
                    :items="businessList"
                    item-text="name"
                    item-value="name"
                    :color="currentBussinesClub.theme.primary"
                    v-bind:label="$t('message.selectBusiness')"
                    hide-details
                    v-model="businessName"
                  ></v-autocomplete>
                </v-flex>
                <v-flex xs12 md12 lg12 class="profile-flex-padding">
                  <v-text-field
                    :color="currentBussinesClub.theme.primary"
                    v-bind:label="$t('message.commercialName')"
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
      getObjectPath: Vue.getObjectPath,
      businessPartnerDialog: false,
      businessPartnerIndex: 1,
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
    openBusinessPartnerDialog(index) {
      this.businessPartnerIndex = index;
      this.businessPartnerDialog = true;
    },
    closeBusinessPartnerDialog() {
      this.businessPartnerDialog = false;
    },
    closeBusinessPartnerDialog() {
      this.businessPartnerDialog = false;
    },
    closeDialog() {
      this.businessPartnerDialog = false;
    },
    activateEditButton() {
      this.activePencil = true;
    },

    cancelEditButton() {
      this.activePencil = false;
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
