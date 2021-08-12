<template>
<v-flex xs12 md6 lazy>
    <v-card class="colorBorderCards" color="transparent" :class="{'mr05': $vuetify.breakpoint.mdAndUp}">
        <v-card-title>
            <span class="titleCard darker-titles">{{$t('message.certifications')}}</span>
            <v-btn small :color="currentBussinesClub.theme.primary" fab dark absolute right @click="openCertificationDialog(-1)">
                <v-icon>add</v-icon>
            </v-btn>
        </v-card-title>
        <v-card-text>
            <v-container class="no-padding">
                <v-layout column fill-height v-if="certifications.length<1">
                    <span class="noContent">{{$t('message.emptyCertification')}}</span>
                </v-layout>
                <v-timeline dense clipped v-else>
                    <v-slide-x-transition group>
                        <v-timeline-item v-for="(item, index) in sortedCertifications" :key="index" class="mb-3" :color="currentBussinesClub.theme.primary">
                            <template v-slot:icon>
                                <v-btn flat icon color="white" @click="openCertificationDialog(index)">
                                    <v-icon small>edit</v-icon>
                                </v-btn>
                            </template>
                            <v-layout justify-space-between>
                                <v-flex xs12>
                                    <v-list-tile-content>
                                        <v-list-tile-title class="titleJob darker-titles" v-html="item.name">
                                        </v-list-tile-title>
                                        <v-list-tile-sub-title class="darker-titles" v-html="item.institution"></v-list-tile-sub-title>
                                        <v-list-tile-sub-title class="darker-titles" v-html="item.year">
                                        </v-list-tile-sub-title>
                                    </v-list-tile-content>
                                </v-flex>
                            </v-layout>
                        </v-timeline-item>
                    </v-slide-x-transition>
                </v-timeline>
            </v-container>
        </v-card-text>
        <v-popup-dialog ref="certification" :model="certificationDialog" :title="indexCertification<0?$t('message.addCertification'):$t('message.editCertification')" showRequired :showRemove="indexCertification>=0" @onClose="closeCertification"
            @onSave="saveCertification" @onRemove="askDeleteCertification">
            <v-text-field ref="certificationText" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.addCertificationLabel')+'*'" v-model="certification.name" :rules="certificationRequiredName">
            </v-text-field>
            <v-text-field :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.institute')+'*'" v-model="certification.institution" :rules="certificationRequiredInstitution"></v-text-field>
            <v-select :no-data-text="$t('message.nodatafound')" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.yearcourse')+'*'" append-icon="event" :items="yearsArray" v-model="certification.year"
                :rules="certificationRequiredYear"></v-select>
        </v-popup-dialog>
        <v-confirm-dialog v-bind:titleToolbar="$t('message.discardChanges')" ref="discardCertification" type="question" :heading="$t('message.changesWithoutSaving')" v-bind:message="$t('message.discardChangesLabelCertification')"
            @onConfirm="discardCertification()">
        </v-confirm-dialog>
        <v-confirm-dialog v-bind:titleToolbar="$t('message.deleteCertification')" ref="deleteCertification" type="question" :heading="$t('message.deleteCertification')"
            v-bind:message="$t('message.deleteCertificationLabel',{certification: certification.name})" @onConfirm="deleteCertification()"></v-confirm-dialog>
    </v-card>
</v-flex>
</template>

<script>
import Vue from "vue";
import confirmDialog from "Components/ySocialHome/confirmDialog";
import {
    mapGetters
} from "vuex";

export default {
    components: {
        "v-confirm-dialog": confirmDialog
    },
    props: {
        'certifications': Array,
    },
    data() {
        return {
            certificationRules: [],
            indexCertification: -1,
            certificationDialog: false,
            certificationDialogModified: false,
            certification: {
                name: "",
                institution: "",
                year: ""
            },
            yearsArray: [],
            settings: {
                maxScrollbarLength: 160
            }
        };
    },
    computed: {
        ...mapGetters(["currentBussinesClub"]),
        sortedCertifications: function() {
            return this.certifications.sort((a, b) => {
                if (a.year > b.year)
                    return -1;
                if (a.year < b.year)
                    return 1;
                if (a.name < b.name)
                    return -1;
                if (a.name > b.name)
                    return 1;
                if (a.institution < b.institution)
                    return -1;
                if (a.institution > b.institution)
                    return 1;
                return 0;
            });
        },
    },
    watch: {
        /* Certification dialog watch fields */
        'certification.name'(value) {
            this.certificationDialogModified = true;
        },
        'certification.institution'(value) {
            this.certificationDialogModified = true;
        },
        'certification.year'(value) {
            this.certificationDialogModified = true;
        },
    },
    created() {
        this.certificationRequiredName = [v => !!v || '* ' + this.$t('message.certificationRequiredName')];
        this.certificationRequiredInstitution = [v => !!v || '* ' + this.$t('message.certificationRequiredInstitution')];
        this.certificationRequiredYear = [v => !!v || '* ' + this.$t('message.certificationRequiredYear')];

        let end = new Date().getFullYear();
        for (let year = end; year >= 1950; year--) {
            this.yearsArray.push(year);
        }
    },
    methods: {
        /**
         * Open the certification dialog
         */
        openCertificationDialog(index) {
            this.indexCertification = index;
            if (index < 0) {
                this.certification.name = "";
                this.certification.institution = "";
                this.certification.year = ""
            } else {
                this.certification.name = this.certifications[index].name;
                this.certification.institution = this.certifications[index].institution;
                this.certification.year = this.certifications[index].year;
            }
            this.$refs.certification.doReset()
            this.certificationDialogModified = false;
            this.certificationDialog = true
        },

        /**
         * Discard the changes into the certification dialog
         */
        discardCertification() {
            this.$refs.discardCertification.close();
            this.certificationDialog = false;
        },

        /**
         * Close the active certification dialog
         */
        closeCertification() {
            if (this.certificationDialogModified) {
                this.$refs.discardCertification.openDialog();
            } else {
                this.certificationDialog = false;
            }
        },

        /**
         * Save the certification dialog information
         */
        saveCertification() {
            if (!this.$refs.certification.doValidate()) {
                return;
            }

            if (this.indexCertification >= 0) {
                this.updateCertification(() => {
                    this.certificationDialog = false;
                });
            } else {
                this.addCertification(() => {
                    this.certificationDialog = false;
                });
            }
        },

        /**
         * Show a popup to confirm the certification removal
         */
        askDeleteCertification() {
            if (this.indexCertification < 0) {
                return;
            }

            this.$refs.deleteCertification.openDialog();
        },

        /**
         * Delete the current certification
         */
        deleteCertification() {
            if (this.indexCertification < 0) {
                return;
            }

            /* Update into the Business Club */
            this.$refs.deleteCertification.close();
            this.removeCertification(() => {
                this.certificationDialog = false;
            });
        },

        /**
         * Add a new certification
         */
        addCertification(cb) {
            const certification = {
                name: this.certification.name,
                institution: this.certification.institution,
                year: this.certification.year,
            };

            this.dispatchAction("doProfileAddCertification", certification, cb);
        },

        /**
         * Update an existant certification
         */
        updateCertification(cb) {
            const certification = {
                certificationId: this.certifications[this.indexCertification]._id,
                name: this.certification.name,
                institution: this.certification.institution,
                year: this.certification.year,
            };

            this.dispatchAction("doProfileUpdateCertification", certification, cb);
        },

        /**
         * Remove a certification
         */
        removeCertification(cb) {
            const certification = {
                certifications: [this.certifications[this.indexCertification]._id],
            };
            this.dispatchAction("doProfileRemoveCertification", certification, cb);
        },

        /**
         * Execute an action on certifcations
         */
        dispatchAction(action, data, cb) {
            this.$store.dispatch(action, data)
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
                    if (cb) {
                        cb();
                    }
                });
        },
    }
};
</script>
