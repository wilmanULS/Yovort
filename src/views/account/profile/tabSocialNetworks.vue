<template>
<v-form onSubmit="return false;" ref="form" v-model="valid" lazy-validation lazy>
    <div>
        <v-card flat color="transparent">
            <v-layout row wrap>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp}" id="demo" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-facebook-f" v-bind:label="$t('message.accountFb')"
                        v-model="facebook" hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('facebook')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'ml05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-instagram" v-bind:label="$t('message.accountIn')" v-model="instagram"
                        hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('instagram')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
            </v-layout>
            <v-layout row wrap>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-youtube" v-bind:label="$t('message.accountYt')" v-model="youtube"
                        hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('youtube')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'ml05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-tictoc" v-bind:label="$t('message.accountTictoc')" v-model="tictoc"
                        hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('tictoc')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
            </v-layout>
            <v-layout row wrap>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-linkedin-in" v-bind:label="$t('message.accountLd')" v-model="linkedin"
                        hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('linkedin')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'ml05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-tumblr" v-bind:label="$t('message.accountTb')" v-model="tumblr"
                        hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('tumblr')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
            </v-layout>
            <v-layout row wrap>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-twitter" v-bind:label="$t('message.accountTw')" v-model="twitter"
                        hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('twitter')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'ml05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-snapchat" v-bind:label="$t('message.accountSn')" v-model="snapchat"
                        hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('snapchat')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
            </v-layout>
            <v-layout row wrap>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-reddit" v-bind:label="$t('message.accountRdt')" v-model="reddit"
                        hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('reddit')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'ml05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-skype" v-bind:label="$t('message.accountSk')" v-model="skype" hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('skype')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
            </v-layout>
            <v-layout row wrap>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-pinterest" v-bind:label="$t('message.accountPt')" v-model="pinterest"
                        hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('pinterest')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
                <v-flex xs12 md6 class="profile-flex-padding">
                    <v-text-field :class="{'ml05': $vuetify.breakpoint.mdAndUp}" @keyup="activeButton()" :color="currentBussinesClub.theme.primary" prepend-inner-icon="fab fa-slideshare" v-bind:label="$t('message.accountSd')" v-model="slideshare"
                        hide-details>
                        <template v-slot:append>
                            <v-icon @click="paste('slideshare')" small>fas fa-paste</v-icon>
                        </template>
                    </v-text-field>
                </v-flex>
            </v-layout>
            <v-layout align-end justify-end row fill-height>
                <v-flex xs12 md2 class="profile-flex-padding">
                    <v-btn @click="clearFields()" dark block large :color="currentBussinesClub.theme.secondary" class="rounded-buttons">{{$t('message.restartAll')}}</v-btn>
                </v-flex>
                <v-flex xs12 md2 class="profile-flex-padding">
                    <v-btn block large :color="currentBussinesClub.theme.primary" :loading="loadingChanges" :disabled="disabledButon" :dark="activeDark" class="rounded-buttons" @click="updateData">{{$t('message.saveChanges')}}</v-btn>
                </v-flex>
            </v-layout>
        </v-card>
    </div>
</v-form>
</template>

<script>
import Vue from "vue";
import {
    mapGetters
} from "vuex";
import {
    EventBus
} from "../../../eventBus.js";
import {
    constants
} from "crypto";
export default {
    props: {
        currentTab: Number
    },
    watch: {
        currentTab(value) {
            if (value === 1) {
                this.reloadValues();
                this.resetChanges();
            }
        }
    },
    created() {
        EventBus.$on("social_networks:saveChanges", data => {
            this.updateData();
        });
        EventBus.$on("social_networks:cancelChanges", data => {
            this.resetChanges();
        });
    },
    mounted() {
        this.reloadValues();
    },
    data() {
        return {
            activeDark: false,
            disabledButon: true,
            loadingChanges: false,
            valid: true,

            /* Social networks */
            facebook: null,
            instagram: null,
            youtube: null,
            tictoc: null,
            linkedin: null,
            tumblr: null,
            twitter: null,
            snapchat: null,
            reddit: null,
            skype: null,
            pinterest: null,
            slideshare: null
        };
    },
    methods: {
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
            this.tictoc = Vue.getObjectPath(
                this.userProfile,
                "social_network.tictoc",
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
            this.$refs.form.reset();
        },
        activeButton() {
            this.activeDark = true;
            this.disabledButon = false;
            this.$store.dispatch("activateProfileChanges", {
                view: "social_networks"
            });
        },
        resetChanges() {
            this.activeDark = false;
            this.disabledButon = true;
            this.$store.dispatch("resetProfileChanges");
        },
        updateData() {
            this.loadingChanges = true;
            let socialNetworks = {
                facebook: this.facebook,
                instagram: this.instagram,
                youtube: this.youtube,
                tictoc: this.tictoc,
                linkedin: this.linkedin,
                tumblr: this.tumblr,
                twitter: this.twitter,
                snapchat: this.snapchat,
                reddit: this.reddit,
                skype: this.skype,
                pinterest: this.pinterest,
                slideshare: this.slideshare
            };

            this.$store.dispatch("updateProfileSocialNetworks", socialNetworks).then(data => {
                this.$store.dispatch("doNotification", {
                    msg: this.$t('message.infoUpdate'),
                    type: 'success'
                });
                this.reloadValues();
                this.resetChanges();
                this.loadingChanges = false;
            }).catch(() => {
                this.$store.dispatch("doNotification", {
                    msg: this.$t("message.sessionErrorDefault"),
                    type: 'error'
                });
            });
        }
    },
    computed: {
        ...mapGetters(["userProfile", "currentBussinesClub"]),
        userProfile: {
            get() {
                if (this.$store.getters.userProfile != "")
                    return JSON.parse(this.$store.getters.userProfile);
            }
        }
    }
};
</script>
