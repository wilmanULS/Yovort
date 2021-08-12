<template>
<v-flex xs12 md12>
    <v-card class="colorBorderCards" color="transparent" style="heigth: 100%!important;">
        <v-card-title>
            <span class="titleCard darker-titles">{{$t('message.certificationsPrices')}}</span>
            <v-btn small :color="currentBussinesClub.theme.primary" fab dark absolute right @click="openCertificationDialog(-1)">
                <v-icon>add</v-icon>
            </v-btn>
        </v-card-title>
        <v-card-text>
            <v-container class="no-padding">
                <v-layout column fill-height v-if="certificationsAwards.length<1">
                    <span class="noContent">{{$t('message.emptyCertification')}}</span>
                </v-layout>
                <v-timeline style="margin-left: 8px;" dense clipped v-else>
                    <v-slide-x-transition group>
                        <v-timeline-item style="margin-left:15px !important;margin-bottom:25px !important" v-for="(item, index) in sortedCertifications" :key="index"  :color="currentBussinesClub.theme.primary">
                            <template v-slot:icon>
                                <!-- <v-btn flat icon color="white" @click="openCertificationDialog(index)">
                                    <v-icon small>edit</v-icon>
                                </v-btn> -->
                                <!-- <v-flex xs12>
                                  <v-avatar size="96" class="profile" @mouseenter="activateEditButton" @mouseleave="cancelEditButton">
                                      <v-img class="user-avatar-desktop" :src="getAwardImage(96,128,item.urlImage)" lazy-src="/static/img/perfil_yovirt_small.png">
                                          <v-container class="animated fadeIn no-padding" fill-height style="background-color:rgba(0,0,0,0.7); cursor:pointer;" v-if="activePencil" @click="openCertificationDialog(index)">
                                              <v-layout align-center justify-center column fill-height>
                                                  <v-btn icon dark class="animated slideInUp">
                                                      <v-icon>mdi mdi-square-edit-outline</v-icon>
                                                  </v-btn>
                                                  <h5 class="white--text animated slideInUp"> {{$t('message.edit')}}</h5>
                                              </v-layout>
                                          </v-container>
                                      </v-img>
                                  </v-avatar>
                              </v-flex> -->
                              <v-avatar  class="awardImage" size="80"  @mouseenter="activateEditButton" @mouseleave="cancelEditButton">
                              <v-img class="user-avatar-desktop" src="http://i.pravatar.cc/64" lazy-src="/static/img/perfil_yovirt_small.png">
                                   <v-container class="animated fadeIn no-padding" fill-height style="background-color:rgba(0,0,0,0.7); cursor:pointer;" v-if="activePencil" @click="openCertificationDialog(index)">
                                              <v-layout align-center justify-center column fill-height>
                                                  <v-btn icon dark class="animated slideInUp">
                                                      <v-icon>mdi mdi-square-edit-outline</v-icon>
                                                  </v-btn>
                                                  <h5 class="white--text animated slideInUp"> {{$t('message.edit')}}</h5>
                                              </v-layout>
                                      </v-container>
                              </v-img>
                            </v-avatar>
                            </template>
                            <v-layout justify-space-between>
                                <v-flex xs12>
                                    <v-list-tile-content>
                                        <v-list-tile-title class="titleJob darker-titles" v-html="item.tittle">
                                        </v-list-tile-title>
                                        <v-list-tile-sub-title class="darker-titles" v-html="item.description"></v-list-tile-sub-title>
                                        <v-list-tile-sub-title class="darker-titles" v-html="item.date">
                                        </v-list-tile-sub-title>
                                    </v-list-tile-content>
                                </v-flex>
                            </v-layout>
                        </v-timeline-item>
                    </v-slide-x-transition>
                </v-timeline>
            </v-container>
        </v-card-text>
        <!-- <v-confirm-dialog v-bind:titleToolbar="$t('message.discardChanges')" ref="discardCertification" type="question" :heading="$t('message.changesWithoutSaving')" v-bind:message="$t('message.discardChangesLabelCertification')"
            @onConfirm="discardCertification()">
        </v-confirm-dialog> -->
        <!-- <v-confirm-dialog v-bind:titleToolbar="$t('message.deleteCertification')" ref="deleteCertification" type="question" :heading="$t('message.deleteCertification')"
            v-bind:message="$t('message.deleteCertificationLabel',{certification: certification.name})" @onConfirm="deleteCertification()"></v-confirm-dialog> -->
    </v-card>
</v-flex>
</template>
<script>
import Vue from "vue";

import { createEditor } from "vueditor";

import { mapGetters } from "vuex";

import DialogBusinessPartner from "./dialogBusinessPartners";

import DialogCertificationsPrices from "./dialogCertificationsPrices";

import PerfectScrollbar from "perfect-scrollbar";

import Certifications from "./certificationsAwards";

export default {
  components: {
    "v-business-partner": DialogBusinessPartner,
    "v-certifications-prices": DialogCertificationsPrices,
    "v-certifications-list": Certifications
  },
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "currentUserBusiness"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    },
    sortedCertifications: function() {
            return this.certificationsAwards.sort((a, b) => {
                if (a.date > b.date)
                    return -1;
                if (a.date < b.date)
                    return 1;
                if (a.tittle < b.tittle)
                    return -1;
                if (a.tittle > b.tittle)
                    return 1;
                return 0;
            });
        }
  },

  created() {
    this.$nextTick(() => {
      this.initEditors();
      var element = document.getElementById("scrollBarPartners");
      new PerfectScrollbar(element, {
        useBothWheelAxes: true
      });
    });
  },
  methods: {
    initEditors() {
      this.textEditorWhoWeAre = createEditor(
        "#editorContainerWhoWeAre",
        this.configEditText
      );
      this.textEditorWhatWeDo = createEditor(
        "#editorContainerWhatWeDo",
        this.configEditText
      );
      this.textEditorMission = createEditor(
        "#editorContainerMission",
        this.configEditText
      );
      this.textEditorVission = createEditor(
        "#editorContainerVission",
        this.configEditText
      );
    },
    activateEditButton() {
            this.activePencil = true;
        },

        cancelEditButton() {
            this.activePencil = false;
        },
    removeTag(item) {
      this.selectTags.splice(this.selectTags.indexOf(item), 1);
      this.selectTags = [...this.selectTags];
    },
    openBusinessPartnerDialog(index) {
      this.$refs.businessPartner.openBusinessPartnerDialog(index);
    },
    openCertificationsPricesDialog(index) {
      this.$refs.certificationsPrices.openCertificationsPriceDialog(index);
    },
    paste(model) {
			navigator.clipboard.readText().then(clipText => {
				this[model] = clipText;
			});
		},
		reloadValues() {
			this.facebook = Vue.getObjectPath(
				this.userProfile,
				"social_network.facebook",
				""
			);
			this.instagram = Vue.getObjectPath(
				this.userProfile,
				"social_network.instagram",
				""
			);
			this.youtube = Vue.getObjectPath(
				this.userProfile,
				"social_network.youtube",
				""
			);
			this.google = Vue.getObjectPath(
				this.userProfile,
				"social_network.google",
				""
			);
			this.linkedin = Vue.getObjectPath(
				this.userProfile,
				"social_network.linkedin",
				""
			);
			this.tumblr = Vue.getObjectPath(
				this.userProfile,
				"social_network.tumblr",
				""
			);
			this.twitter = Vue.getObjectPath(
				this.userProfile,
				"social_network.twitter",
				""
			);
			this.snapchat = Vue.getObjectPath(
				this.userProfile,
				"social_network.snapchat",
				""
			);
			this.reddit = Vue.getObjectPath(
				this.userProfile,
				"social_network.reddit",
				""
			);
			this.skype = Vue.getObjectPath(
				this.userProfile,
				"social_network.skype",
				""
			);
			this.pinterest = Vue.getObjectPath(
				this.userProfile,
				"social_network.pinterest",
				""
			);
			this.slideshare = Vue.getObjectPath(
				this.userProfile,
				"social_network.slideshare",
				""
			);
		},
		clearFields() {
			/* this.$refs.form.reset(); */
		},
		activeButton() {
			this.activeDark = true;
			this.disabledButon = false;
		/* 	this.$store.dispatch("activateProfileChanges", {
				view: "social_networks"
			}); */
		},
		resetChanges() {
			this.activeDark = false;
			this.disabledButon = true;/* 
			this.$store.dispatch("resetProfileChanges"); */
		},
		updateData() {
			/* this.loadingChanges = true;
			let dataProfileAPI = {
				type: "social_network",
				data: {
					facebook: this.facebook,
					instagram: this.instagram,
					youtube: this.youtube,
					google: this.google,
					linkedin: this.linkedin,
					tumblr: this.tumblr,
					twitter: this.twitter,
					snapchat: this.snapchat,
					reddit: this.reddit,
					skype: this.skype,
					pinterest: this.pinterest,
					slideshare: this.slideshare
				}
			};

			this.$store.dispatch("updateProfile", dataProfileAPI).then(data => {
				this.reloadValues();
				this.resetChanges();
				this.loadingChanges = false;
			}); */
    },
    getAwardImage(realSize, size, url) {
      return url
            /* return `${AppConfig.FMS_URL}/image/fetch/${Vue.getObjectPath(this.userProfile, 'personal_info.picture','/static/img/perfil_yovirt.png')}?tx=resize(w:${realSize},h:${realSize})&username=${Vue.getObjectPath(this.userProfile, 'personal_info.names','')} ${Vue.getObjectPath(this.userProfile, 'personal_info.surnames','')}&color=${Vue.getObjectPath(this.userProfile, 'personal_info.color','no')}&size=${size}`
       */  }
  },
  data() {
    return {
      getObjectPath: Vue.getObjectPath,
      activePencil:false,
      certificationsAwards:[{
        urlImage:"",
        tittle:"Premio HTC",
        date:"2018-05-25",
        description:"Premio HTC"
      },{
        urlImage:"",
        tittle:"Premio JL",
        date:"2017-06-06",
        description:"Premio JL Tommatoes"
      },{
        urlImage:"",
        tittle:"Premio FL",
        date:"2019-01-25",
        description:"Premio FL"
      },{
        urlImage:"",
        tittle:"Premio PSP",
        date:"2018-12-12",
        description:"Premio PSP 2018"
      }],
      textEditorWhoWeAre: null,
      textEditorWhatWeDo: null,
      textEditorMission: null,
      textEditorVission: null,
      selectTags: [],
      businessPartners: [
        {
          avatar: "",
          logo: "",
          businessName: "Test"
        },
        {
          avatar: "",
          logo: "",
          businessName: "Test1"
        },
        {
          avatar: "",
          logo: "",
          businessName: "Test2"
        },
        {
          avatar: "",
          logo: "",
          businessName: "Test1"
        },
        {
          avatar: "",
          logo: "",
          businessName: "Test2"
        },
        {
          avatar: "",
          logo: "",
          businessName: "Test1"
        },
        {
          avatar: "",
          logo: "",
          businessName: "Test2"
        }
      ],
      activeDark: false,
			disabledButon: true,
			loadingChanges: false,
			valid: true,

			/* Social networks */
			facebook: null,
			instagram: null,
			youtube: null,
			google: null,
			linkedin: null,
			tumblr: null,
			twitter: null,
			snapchat: null,
			reddit: null,
			skype: null,
			pinterest: null,
			slideshare: null
    };
  }
};
</script>
<style>
.awardImage{
      border-radius: 50% !important;
}
</style>

