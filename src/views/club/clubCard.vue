<template>
<v-card class="flexcard" style="margin: .5rem!important;" :title="club.club" lazy>
    <v-img style="background: #2C353A!important;" max-height="150px" min-height="150px" height="150px" :src="getImage(club.logo_url_large || club.logo_full)" contain>
        <span style="position: absolute!important;left: 5px!important; top:5px!important; z-index: 1000!important;color: white;font-size: 11px;">
            <span v-if="getObjectPath(club, 'membership.status_flag_catalog', null)===2">{{$t('message.approve')}}</span>
            <span v-if="getObjectPath(club, 'membership.status_flag_catalog', null)===0">{{$t('message.pending')}}</span>
            <span v-if="getObjectPath(club, 'membership.status_flag_catalog', null)===4">{{$t('message.rejectUser')}}</span>
            <span v-if="getObjectPath(club, 'membership.status_flag_catalog', null)===6">{{$t('message.block')}}</span>
            <span v-if="getObjectPath(club, 'membership.status_flag_catalog', null)===8">{{$t('message.invitedMember')}}</span>
            <span v-if="getObjectPath(club, 'membership.status_flag_catalog', null)===null">{{$t('message.noRequested')}}</span>
        </span>
        <div style="position: absolute!important;right: 5px!important; bottom:5px!important; z-index: 1000!important;">
            <v-speed-dial v-model="fab" direction="left" open-on-hover transition="slide-x-reverse-transition">
                <template v-slot:activator>
                    <v-btn v-model="fab" :color="currentBussinesClub.theme.secondary" dark fab small>
                        <v-icon>mdi mdi-plus</v-icon>
                        <v-icon>mdi mdi-close</v-icon>
                    </v-btn>
                </template>
                <v-btn :title="$t('message.clubRequestMembership')" fab dark small :color="currentBussinesClub.theme.primary"
                    v-if="getObjectPath(club,'settings.members.registering') && (club.settings.members.registering==2 ||club.settings.members.registering==3) && getObjectPath(club, 'membership.status_flag_catalog', null)===null"
                    @click="joinvClub(club)">
                    <v-icon>mdi mdi-account-plus</v-icon>
                </v-btn>
                <v-btn :title="$t('message.clubVisit')" fab dark small :color="currentBussinesClub.theme.primary" @click="gotoClub(club.subdomain)" v-if="getObjectPath(club, 'membership.status_flag_catalog', null)===2">
                    <v-icon>mdi mdi-web</v-icon>
                </v-btn>
                <v-btn :title="$t('message.clubRules')" fab dark small :color="getObjectPath(club, 'membership.status_flag_catalog', null)===0? currentBussinesClub.theme.primary : currentBussinesClub.theme.secondary" @click="showRules(club.rules)">
                    <v-icon>mdi mdi-format-list-numbered</v-icon>
                </v-btn>
                <v-btn :title="$t('message.clubMoreInformation')" fab dark small :color="getObjectPath(club, 'membership.status_flag_catalog', null)===0? currentBussinesClub.theme.secondary : currentBussinesClub.theme.primary"
                    @click="showMoreInfo(club)">
                    <v-icon>mdi mdi-information</v-icon>
                </v-btn>
            </v-speed-dial>
        </div>
    </v-img>
    <v-card-text class="grow p8" primary-title>
        <div style="color:black!important; overflow: hidden;text-overflow: ellipsis;white-space: nowrap;" class="headline mb-0">{{club.club}}</div>
    </v-card-text>
    <v-popup-dialog :model="rulesClub" :title="$t('message.businessClubRulesTitle')" hideButtons @onClose="closeRules">
        <v-layout column fill-height>
            <vue-perfect-scrollbar ref="scrollbar" class="scroll-area">
                <v-flex style="text-align:justify;color: black!important;">
                    <ol v-if="viewRules.length>0">
                        <li class="custom-li" v-for="(rule, i) in viewRules" :key="i">
                            <span v-html="rule.descriptionRule"></span>
                        </li>
                    </ol>
                    <span v-else>{{$t("message.noDataRulesMessage")}}</span>
                </v-flex>
                <v-flex>
                    <v-layout align-center justify-center row fill-height>
                        <v-flex xs3 class="pa-2">
                            <v-btn :color="currentBussinesClub.theme.primary" dark round @click="closeRules()">Aceptar</v-btn>
                        </v-flex>
                    </v-layout>
                </v-flex>
            </vue-perfect-scrollbar>
        </v-layout>
    </v-popup-dialog>
    <v-dialog :fullscreen="$vuetify.breakpoint.xsOnly" v-model="moreInformation" max-width="40%" persistent>
        <v-card tile v-if="targetClub">
            <v-img style="background: #2C353A!important;" max-height="150px" min-height="150px" height="150px" :src="getImage(targetClub.logo_url_large || targetClub.logo_full)" contain>
                <v-btn icon small absolute right @click="closeMoreInfo" class="mt-1">
                    <v-icon class="fs-24 white--text">mdi mdi-close</v-icon>
                </v-btn>
                <span v-if="getObjectPath(targetClub, 'membersApproved', 0)>0" style="position: absolute!important;right: 5px!important; bottom:5px!important; z-index: 1000!important;color: white;font-size: 11px;">
                    <span>{{$t('message.amountMembers',{members: getObjectPath(targetClub, 'membersApproved', 0)})}}</span>
                </span>
            </v-img>
            <v-card-text class="grow p8" primary-title>
                <h3 style="color:black!important; overflow: hidden;text-overflow: ellipsis;white-space: nowrap;" class="headline mb-0">{{targetClub.club}}</h3>
                <span style="color:black!important;">{{targetClub.description}}</span>
            </v-card-text>
        </v-card>
    </v-dialog>
</v-card>
</template>
<script>
import {
    mapGetters
} from "vuex";
import AppConfig from "Constants/AppConfig.js";
import Vue from "vue";
export default {
    props: {
        club: Object,
    },
    components: {

    },
    computed: {
        ...mapGetters([
            "userProfile",
            "currentBussinesClub",
            "userId",
            "userCredential",
            "userCredentialRefresh",
        ]),
        userProfile: {
            get() {
                if (this.$store.getters.userProfile != '')
                    return JSON.parse(this.$store.getters.userProfile)
            }
        },
    },
    data() {
        return {
            fab: false,
            getObjectPath: Vue.getObjectPath,
            FMS_URL: AppConfig.FMS_URL,
            viewRules: [],
            rulesClub: false,
            moreInformation: false,
            targetClub: null,
        };
    },
    methods: {
        getImage(image) {
            if (!image) {
                return null;
            }
            if (image.startsWith('http')) {
                return image;
            }
            return `${AppConfig.FMS_URL}/image/fetch/${image}`;
        },
        joinvClub(club) {
            this.$store
                .dispatch("requestCreateMembership", {
                    email: Vue.getObjectPath(this.userProfile, "personal_info.email", null),
                    business_club_id: club.clubId
                })
                .then(() => {
                    this.$store.dispatch("doNotification", {
                        msg: this.$t("message.clubMembershipRequested"),
                        type: 'success'
                    });
                    this.$store.dispatch("doGetMyvClubs").then(() => {});
                })
                .catch(err => {
                    this.$store.dispatch("doNotification", {
                        msg: this.$t("message.clubMembershipRequestedError"),
                        type: 'error'
                    });
                });
        },
        gotoClub(subdomain) {
            let url = "https://" + ((subdomain === 'yovirt') ? "www" : `${subdomain}`) + `.${AppConfig.BASE_URL}`;
            window.open(
                `${url}` +
                "/session/login?VATUP=" +
                this.userCredential +
                "&VRTUP=" +
                this.userCredentialRefresh +
                "&userId=" +
                this.userId);
        },
        showRules(rules) {
            this.viewRules = rules;
            this.rulesClub = true;
        },
        closeRules() {
            this.rulesClub = false;
            this.$nextTick(() => {
                this.viewRules = [];
            });
        },
        showMoreInfo(club) {
            this.targetClub = club;
            this.moreInformation = true;
        },
        closeMoreInfo() {
            this.moreInformation = false;
            this.$nextTick(() => {
                this.targetClub = null;
            });
        },
    }
};
</script>
<style scoped>
.flexcard {
    display: flex !important;
    flex-direction: column !important;
}

.grow {
    flex-grow: 1 !important;
    align-items: flex-start !important;
}

.p8 {
    padding: 8px !important;
}
</style>
