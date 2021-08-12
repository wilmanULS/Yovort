<template>
  <v-flex>
    <!-- Business information-->
    <v-layout row wrap>
      <v-flex xs12>
        <span class="vb-cp-title">{{$t('message.vBusinessInformation')}}</span>
        <span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessInformationDetail')}}</span>
      </v-flex>
    </v-layout>

    <v-layout row wrap>
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-flex
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
        >
          <v-layout row wrap>
            <v-flex xs12>
              <v-layout fill-height row wrap justify-center align-center>
                <v-flex xs7>
                  <span class="titleCard darker-titles">{{$t('message.whoWeAreQuestion')}}</span>
                </v-flex>
                <v-flex xs4>
                  <span style="font-size:11px!important; color:black">Publicado en el sitio web</span>
                </v-flex>
                <v-flex xs1>
                  <v-switch
                    :color="currentBussinesClub.theme.primary"
                    v-model="whoWeAreCheck"
                    @change="savePublishTopic('whoWeAre',whoWeAreCheck)"
                  ></v-switch>
                </v-flex>
              </v-layout>
            </v-flex>
          </v-layout>
          <vue-editor v-model="whoWeAre"></vue-editor>
          <!--    <v-card
            :class="$vuetify.breakpoint.smAndDown?'mobileEditor':'desktopEditor'"
            flat
            style="border-style: solid; border-width: 1px; border-color: #cfd8dc;"
          >
          
            <div id="editorContainerWhoWeAre" ref="editorContainerWhoWeAre"></div>  
          </v-card>-->
        </v-flex>
      </v-flex>
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-flex
          :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
        >
          <v-layout row wrap>
            <v-flex xs12>
              <v-layout fill-height row wrap justify-center align-center>
                <v-flex xs7>
                  <span class="titleCard darker-titles">{{$t('message.whatWeDoQuestion')}}</span>
                </v-flex>
                <v-flex xs4>
                  <span style="font-size:11px!important; color:black">Publicado en el sitio web</span>
                </v-flex>
                <v-flex xs1>
                  <v-switch
                    :color="currentBussinesClub.theme.primary"
                    @change="savePublishTopic('whatWeDo',whatWeDoCheck)"
                    v-model="whatWeDoCheck"
                  ></v-switch>
                </v-flex>
              </v-layout>
            </v-flex>
          </v-layout>

          <vue-editor v-model="whatWeDo"></vue-editor>
        </v-flex>
      </v-flex>
    </v-layout>
    <v-layout row wrap>
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-flex
          :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
        >
          <v-layout row wrap>
            <v-flex xs12>
              <v-layout fill-height row wrap justify-center align-center>
                <v-flex xs7>
                  <span class="titleCard darker-titles">{{$t('message.ourMission')}}</span>
                </v-flex>
                <v-flex xs4>
                  <span style="font-size:11px!important; color:black">Publicado en el sitio web</span>
                </v-flex>
                <v-flex xs1>
                  <v-switch
                    :color="currentBussinesClub.theme.primary"
                    v-model="ourMissionCheck"
                    @change="savePublishTopic('ourMission',ourMissionCheck)"
                  ></v-switch>
                </v-flex>
              </v-layout>
            </v-flex>
          </v-layout>

          <vue-editor v-model="ourMission"></vue-editor>
        </v-flex>
      </v-flex>
      <v-flex xs12 md6 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-flex
          :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"
        >
          <v-layout row wrap>
            <v-flex xs12>
              <v-layout fill-height row wrap justify-center align-center>
                <v-flex xs7>
                  <span class="titleCard darker-titles">{{$t('message.ourVission')}}</span>
                </v-flex>
                <v-flex xs4>
                  <span style="font-size:11px!important; color:black">Publicado en el sitio web</span>
                </v-flex>
                <v-flex xs1>
                  <v-switch
                    :color="currentBussinesClub.theme.primary"
                    v-model="ourVisionCheck"
                    @change="savePublishTopic('ourVision',ourVisionCheck)"
                  ></v-switch>
                </v-flex>
              </v-layout>
            </v-flex>
          </v-layout>
          <vue-editor v-model="ourVision"></vue-editor>
        </v-flex>
      </v-flex>
    </v-layout>
    <!-- Business slogan-->
    <v-layout row wrap>
      <v-flex xs12>
        <span class="vb-cp-title">{{$t('message.vBusinessInformationSlogan')}}</span>
        <span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessInformationSloganDetail')}}</span>
      </v-flex>
    </v-layout>

    <v-layout row wrap>
      <v-flex xs12 class="pt-2">
        <v-text-field
          maxLength=80
          ref="title"    
          clearable
          v-model="slogan"
          hide-details         
          :color="currentBussinesClub.theme.primary"
          v-bind:label="$t('message.vBusinessInformationSloganTitle')"
          required          
        ></v-text-field>
      </v-flex>
      <v-flex xs12 class="pt-3">
        <v-layout row wrap>
                <span class="titleCard darker-titles">{{$t('message.vBusinessInformationSloganDescription')}}</span>
        </v-layout>
        <vue-editor v-model="sloganDescription"></vue-editor>
      </v-flex>
    </v-layout>

    <v-layout align-end justify-end row fill-height>
      <v-btn
        dark
        :color="currentBussinesClub.theme.primary"
        @click="saveBusinessAbout()"
      >{{$t('message.save')}}</v-btn>
    </v-layout>
  </v-flex>
</template>

<script>
import Vue from "vue";
import { mapGetters } from "vuex";
import { VueEditor } from "vue2-editor";
export default {
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "businessId"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    }
  },
  components: {
    "vue-editor": VueEditor
  },
  mounted() {
    Vue.nextTick(() => {
      this.$root.$children[0].$emit(
        "showLoading",
        "Cargando información del negocio"
      );
      this.$store
        .dispatch("doGetBusinessControlPanel", {
          businessId: this.businessId,
          menu: "businessAbout"
        })
        .then(res => {
          this.responseBusinessAbout = res.data.data;
          this.reloadFields();
          this.$root.$children[0].$emit("hideLoading");
        })
        .finally(() => {
          if (this.$vuetify.breakpoint.mdAndUp) {
            this.$parent.$parent.$parent.$parent.$parent.$el.scrollTop = "400";
          } else {
            this.$parent.$parent.$parent.$parent.$parent.$el.scrollTop = "0";
          }
        });
    });
  },
  methods: {
    reloadFields() {
      this.whoWeAre = Vue.getObjectPath(
        this.responseBusinessAbout,
        "businessAbout.whoWeAre.description",
        ""
      );
      this.whatWeDo = Vue.getObjectPath(
        this.responseBusinessAbout,
        "businessAbout.whatWeDo.description",
        ""
      );
      this.ourMission = Vue.getObjectPath(
        this.responseBusinessAbout,
        "businessAbout.ourMission.description",
        ""
      );
      this.ourVision = Vue.getObjectPath(
        this.responseBusinessAbout,
        "businessAbout.ourVision.description",
        ""
      );
      this.whoWeAreCheck = Vue.getObjectPath(
        this.responseBusinessAbout,
        "businessAbout.whoWeAre.publish",
        false
      );
      this.whatWeDoCheck = Vue.getObjectPath(
        this.responseBusinessAbout,
        "businessAbout.whatWeDo.publish",
        false
      );
      this.ourMissionCheck = Vue.getObjectPath(
        this.responseBusinessAbout,
        "businessAbout.ourMission.publish",
        false
      );
      this.ourVisionCheck = Vue.getObjectPath(
        this.responseBusinessAbout,
        "businessAbout.ourVision.publish",
        false
      );
       this.slogan = Vue.getObjectPath(
        this.responseBusinessAbout,
        "businessAbout.slogan.title",
        ""
      );
           this.sloganDescription = Vue.getObjectPath(
        this.responseBusinessAbout,
        "businessAbout.slogan.description",
        ""
      );
    },
    savePublishTopic(topic, state) {
      this.$store
        .dispatch("doRegisterBusinessAbout", {
          businessId: this.businessId,
          businessAboutData: {
            type: "publish",
            topic: topic,
            publish: state
          }
        })
        .then(() => {});
    },
    saveBusinessAbout() {
      this.$root.$children[0].$emit(
        "showLoading",
        "Guardando información del negocio"
      );
      this.$store
        .dispatch("doRegisterBusinessAbout", {
          businessId: this.businessId,
          businessAboutData: {
            type: "all",
            businessAbout: {
              whoWeAre: {
                description: this.whoWeAre,
                publish: this.whoWeAreCheck
              },
              ourVision: {
                description: this.ourVision,
                publish: this.ourVisionCheck
              },
              whatWeDo: {
                description: this.whatWeDo,
                publish: this.whatWeDoCheck
              },
              ourMission: {
                description: this.ourMission,
                publish: this.ourMissionCheck
              },
              slogan:{
                title:this.slogan,
                description:this.sloganDescription
              
              }
            }
          }
        })
        .then(() => {
          this.$store.dispatch("doNotification", {
            msg: this.$t("message.vBusinessAboutSucces"),
            type: "success"
          });
        })
        .finally(() => {
          this.$root.$children[0].$emit("hideLoading");
        });
    }
  },
  data() {
    return {
      slogan:"",
      sloganDescription:"",
      whoWeAre: "",
      whatWeDo: "",
      ourMission: "",
      ourVision: "",
      responseBusinessAbout: null,
      whoWeAreCheck: false,
      whatWeDoCheck: false,
      ourMissionCheck: false,
      ourVisionCheck: false
    };
  }
};
</script>
 <style>
.alignleft {
  float: left;
}
.alignright {
  float: right;
}
</style>
 