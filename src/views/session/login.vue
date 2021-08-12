<template>
<div v-bind:style="{'background-image': `linear-gradient(to bottom right, ${currentBussinesClub.theme.secondary}, ${currentBussinesClub.theme.primary})`}">
    <v-container fill-height fluid class="loginContainerLayer yovirt-layer">
        <v-layout row fill-height>
            <div style="position: absolute; top: 10px; right: 20px; z-index:1000;">
                <v-lang-provider></v-lang-provider>
            </div>
            <v-flex v-if="$vuetify.breakpoint.mdAndUp" class="flex-side-picture">
                <div class="sidePicture text-xs-center" v-bind:style="{'background-image':'url(\'/static/img/yovirt-banner-low.png\')'}">
                </div>
            </v-flex>
            <v-flex style="min-height: 100vh!important;" v-bind:class="{'flex-form-full-column':$vuetify.breakpoint.smAndDown, 'flex-form-column':$vuetify.breakpoint.mdAndUp }">
                <v-container>
                    <v-layout align-center justify-center column fill-height>
                        <v-flex></v-flex>
                        <v-flex v-bind:class="{'md-limit-sm':$vuetify.breakpoint.smAndDown, 'md-limit-md':$vuetify.breakpoint.mdAndUp }">
                            <v-card color="transparent" flat>
                                <v-img :src="`${FMS_URL}/image/fetch/${currentBussinesClub.settings.logo_url_large}`" aspect-ratio="3" contain></v-img>
                                <v-card-title primary-title>
                                    <div>
                                        <h3 class="headline mb-2 white--text">{{$t('message.enterYovirt')}}</h3>
                                        <div class="mb-3 white--text">{{$t('message.descriptionYovirt')}}</div>
                                        <v-tabs slot="extension" dark v-model="currentTab" color="transparent" fixed-tabs slider-color="white">
                                            <v-tab dark>
                                                <span class="tabs-font-size">{{$t('message.access')}}</span>
                                            </v-tab>
                                            <v-tab dark>
                                                <span class="tabs-font-size">{{functionTabTitle}}</span>
                                            </v-tab>
                                        </v-tabs>
                                        <v-container fluid class="tabs-items-container">
                                            <v-login-signin :currentTab="currentTab" v-on:confirmAccount="changeToConfirm" v-on:recover="changeToRecover"></v-login-signin>
                                            <v-login-register :currentTab="currentTab" :tabFunction="tabFunction" :vcode="inviteVCode" v-on:confirmAccount="changeToConfirm"></v-login-register>
                                            <v-login-confirm :currentTab="currentTab" :tabFunction="tabFunction" :confirmEmail="confirmEmail" :confirmUserId="confirmUserId" v-on:returnAccess="changeToAccess"></v-login-confirm>
                                            <v-login-recover :currentTab="currentTab" :tabFunction="tabFunction" :initialEmail="confirmEmail" v-on:returnAccess="changeToAccess"></v-login-recover>
                                        </v-container>
                                    </div>
                                </v-card-title>
                            </v-card>
                        </v-flex>
                        <v-flex></v-flex>
                    </v-layout>
                </v-container>
            </v-flex>
        </v-layout>
    </v-container>
</div>
</template>

<script>
import Vue from 'vue';
import {
    mapGetters
} from "vuex";
import ServicesConfig from "../../constants/ServicesConfig";
import router from "../../router";
import AppConfig from "../../constants/AppConfig";
import ERRORS from '../../constants/errors';
import SessionSliderWidget from "Components/widgets/SessionSlider";
import LangProvider from '../../layout/header/langProvider';
import Signin from "./loginSignin";
import Register from "./loginRegister";
import ConfirmAccount from "./loginConfirmAccount";
import RecoverPassword from "./loginRecoverPassword";

export default {
    computed: {
        ...mapGetters([
            "currentBussinesClub",
            "userId",
            "userCredential",
            "userCredentialRefresh",
        ]),
    },
    watch: {
        currentTab(value) {
            /* Reset state if the access tab is selected */
            if (value === 0) {
                this.changeToAccess();
            }
        },
    },
    components: {
        'v-lang-provider': LangProvider,
        "v-login-signin": Signin,
        "v-login-register": Register,
        "v-login-confirm": ConfirmAccount,
        "v-login-recover": RecoverPassword,
        SessionSliderWidget,
    },
    data() {
        return {
            FMS_URL: AppConfig.FMS_URL,
            loaded: false,
            currentTab: 0,
            tabFunction: 0,
            functionTabTitle: this.$t('message.register'),
            inviteVCode: null,
            confirmEmail: null,
            confirmUserId: null,
        };
    },
    created() {

    },
    mounted() {
        /*
         * Listeners to get url from navigator to determinate if an user came
         *  from an external link like referred link or validate email link.
         */
        let url = new URL(window.location.href);
        let referral = url.searchParams.get("ref");

        if (referral) {
            this.tabFunction = 0;
            this.currentTab = 1;
            this.inviteVCode = referral;
        }
    },
    methods: {
        /**
         * Change to default access tab
         */
        changeToAccess() {
            this.functionTabTitle = this.$t('message.register');
            this.tabFunction = 0;
            this.currentTab = 0;
        },

        /**
         * Change to account confirm tab
         */
        changeToConfirm(value) {
            this.functionTabTitle = this.$t('message.confirmAccount')
            this.confirmEmail = value.email;
            this.confirmUserId = value.userId;
            this.tabFunction = 1;
            this.currentTab = 1;
        },

        /**
         * Change to user account recover tab
         */
        changeToRecover(value) {
            this.functionTabTitle = this.$t('message.recoverPassword')
            this.confirmEmail = value.email;
            this.tabFunction = 2;
            this.currentTab = 1;
        },
    }
};
</script>
