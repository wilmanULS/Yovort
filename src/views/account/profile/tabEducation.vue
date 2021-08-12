<template>
<v-container class="no-padding" lazy>
    <v-layout row wrap>
        <v-flex xs12 class="mb-1">
            <v-select @change="saveSchoolLevel(school_level.code)" item-text="name" item-value="code" :items="schoolarship" v-model="school_level" :color="currentBussinesClub.theme.primary" hide-details v-bind:label="$t('message.schoolarship')"
                return-object></v-select>
        </v-flex>
    </v-layout>
    <v-layout row wrap>
        <v-education-profession :professions="this.professions" class="mb-1"></v-education-profession>
    </v-layout>
    <v-layout row wrap>
        <v-education-certification :certifications="this.certifications" :class="{'mb-1':$vuetify.breakpoint.smAndDown }"></v-education-certification>
        <v-education-institution :institutions="this.institutions"></v-education-institution>
    </v-layout>
</v-container>
</template>

<script>
import confirmDialog from "Components/ySocialHome/confirmDialog";
import {
    mapGetters
} from "vuex";
import Vue from "vue";
import EducationCertifications from './tabEducationCertifications';
import EducationProfessions from './tabEducationProfessions';
import EducationInstitutions from './tabEducationInstitutions';
import {
    error
} from 'util';

export default {
    props: {
        'currentTab': Number,
    },
    components: {
        "v-confirm-dialog": confirmDialog,
        "v-education-certification": EducationCertifications,
        "v-education-profession": EducationProfessions,
        "v-education-institution": EducationInstitutions,
    },
    data() {
        return {
            certifications: [],
            professions: [],
            institutions: [],
            school_level: null,
        };
    },
    computed: {
        ...mapGetters(["userProfile", "schoolarship", "currentBussinesClub"]),
        userProfile: {
            get() {
                if (this.$store.getters.userProfile != '')
                    return JSON.parse(this.$store.getters.userProfile);
            }
        },
    },
    watch: {
        userProfile() {
            this.reloadEducationInformation();
        }
    },
    created() {
        this.reloadEducationInformation();
    },
    mounted() {
        this.$store.dispatch("doGetSchoolarship");
    },
    methods: {
        reloadEducationInformation() {
            this.generalEducation = Vue.getObjectPath(this.userProfile, 'educational_info', {});
            this.school_level = parseInt(this.generalEducation.school_level)
            this.certifications = this.generalEducation.certifications || [];
            this.institutions = this.generalEducation.institutions || [];
            this.professions = this.generalEducation.professions || [];
        },

        /**
         * Store the school level information into the Bussines Club service
         */
        saveSchoolLevel(value, cb) {
            const schoolLevel = {
                schoolLevel: value,
            };
            this.$store.dispatch("updateProfileSchoolLevel", schoolLevel)
                .then(() => {
                    this.$store.dispatch("doNotification", {
                        msg: this.$t('message.infoUpdate'),
                        type: 'success'
                    });
                }).catch(() => {
                    this.$store.dispatch("doNotification", {
                        msg: this.$t("message.sessionErrorDefault"),
                        type: 'error'
                    });
                }).finally(() => {
                    this.reloadEducationInformation();
                    if (cb) {
                        cb();
                    }
                });
        },
    }
};
</script>
