<template>
  <v-form data-vv-scope="step3">
    <v-layout row wrap>
      <v-flex xs12 md6>
        <span
          style="text-align: left;padding-bottom: 5px"
          class="titleCard darker-titles"
        >{{$t('message.whoWeAreQuestion')}}</span>
        <v-card
          :class="$vuetify.breakpoint.smAndDown?'mobileEditor':'desktopEditor'"
          flat
          style="border-style: solid; border-width: 1px; border-color: #cfd8dc;"
        >
          <div id="editorContainerWhoWeAre" ref="editorContainerWhoWeAre"></div>
        </v-card>
      </v-flex>
      <v-flex xs12 md6>
        <span
          style="text-align: left;padding-bottom: 5px"
          class="titleCard darker-titles"
        >{{$t('message.whatWeDoQuestion')}}</span>
        <v-card
          :class="$vuetify.breakpoint.smAndDown?'mobileEditor':'desktopEditor'"
          flat
          style="border-style: solid; border-width: 1px; border-color: #cfd8dc;"
        >
          <div id="editorContainerWhatWeDo" ref="WhatWeDo"></div>
        </v-card>
      </v-flex>
  </v-layout>
  <v-layout row wrap>
      <v-flex xs12 md6>
        <span
          style="text-align: left;padding-bottom: 5px"
          class="titleCard darker-titles"
        >{{$t('message.ourMission')}}</span>
        <v-card
          :class="$vuetify.breakpoint.smAndDown?'mobileEditor':'desktopEditor'"
          flat
          style="border-style: solid; border-width: 1px; border-color: #cfd8dc;"
        >
          <div id="editorContainerMission" ref="Mission"></div>
        </v-card>
      </v-flex>
      <v-flex xs12 md6>
        <span
          style="text-align: left;padding-bottom: 5px"
          class="titleCard darker-titles"
        >{{$t('message.ourVission')}}</span>
        <v-card
          :class="$vuetify.breakpoint.smAndDown?'mobileEditor':'desktopEditor'"
          flat
          style="border-style: solid; border-width: 1px; border-color: #cfd8dc;"
        >
          <div id="editorContainerVission" ref="Vission"></div>
        </v-card>
      </v-flex>
  </v-layout>   
  
  <v-certifications-list></v-certifications-list>   
     
  </v-form>
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
		}
  },
  data() {
    return {
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
      slideshare: null,
      configEditText:{
                toolbar: [
                    "removeFormat",
                    "|",
                    "elements",
                    "fontName",
                    "fontSize",
                    "foreColor",
                    "divider",
                    "bold",
                    "italic",
                    "underline",
                    "links",
                    "divider",
                    "justifyLeft",
                    "justifyCenter",
                    "justifyRight",
                    "justifyFull",
                    "|",
                    "indent",
                    "outdent",
                    "insertOrderedList",
                    "insertUnorderedList"
                ],
                fontName: [{
                        val: "arial black"
                    },
                    {
                        val: "times new roman"
                    },
                    {
                        val: "Courier New"
                    }
                ],
                fontSize: [
                    "12px",
                    "14px",
                    "16px",
                    "18px",
                    "20px",
                    "24px",
                    "28px",
                    "32px",
                    "36px"
                ],
                uploadUrl: "",
                classList: ['color:blue']
            }
    };
  }
};
</script>
<style>
.ve-dropdown{
  color: rgba(0,0,0,.6);
}
</style>
