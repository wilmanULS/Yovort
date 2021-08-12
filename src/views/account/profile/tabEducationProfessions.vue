<template>
<v-flex xs12 md12 lazy>
    <v-card class="colorBorderCards" color="transparent">
        <v-card-title>
            <span class="titleCard darker-titles">{{$t('message.profession')}}</span>
            <v-btn small :color="currentBussinesClub.theme.primary" fab dark absolute right @click="openProfessionDialog()">
                <v-icon>add</v-icon>
            </v-btn>
        </v-card-title>
        <v-card-text>
            <v-container class="no-padding">
                <v-layout column fill-height v-if="professions.length<1">
                    <span class="noContent">{{$t('message.emptyProfession')}}</span>
                </v-layout>
                <template>
                    <v-layout row wrap>
                        <div v-for="(profession, index) in professions" :key="index">
                            <v-chip style="background: white!important;" outline :color="currentBussinesClub.theme.primary" :ref="'chip'+index" close @input="askDeleteProfession(index)"><span class="darker-titles">{{profession}}</span></v-chip>
                        </div>
                    </v-layout>
                </template>
            </v-container>
        </v-card-text>
        <v-popup-dialog ref="profession" :model="professionDialog" :title="$t('message.addProfession')" showRequired @onClose="closeProfession" @onSave="saveProfession">
            <v-text-field ref='professionText' :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.addProfessionLabel')+'*'" v-model="profession" :rules="professionRequiredName" @keyup.enter="saveProfession">
            </v-text-field>
        </v-popup-dialog>
        <v-confirm-dialog v-bind:titleToolbar="$t('message.discardChanges')" ref="discardProfession" type="question" :heading="$t('message.changesWithoutSaving')" v-bind:message="$t('message.discardChangesLabelProfession')"
            @onConfirm="discardProfession"></v-confirm-dialog>
        <v-confirm-dialog v-bind:titleToolbar="$t('message.deleteProfession')" ref="deleteProfession" type="question" :heading="$t('message.deleteProfession')"
            v-bind:message="$t('message.deleteProfessionLabel',{profession: professions[indexProfession]})" @onConfirm="deleteProfession"></v-confirm-dialog>
    </v-card>
</v-flex>
</template>

<script>
import confirmDialog from "Components/ySocialHome/confirmDialog";
import {
    mapGetters
} from "vuex";
import Vue from "vue";

export default {
    components: {
        "v-confirm-dialog": confirmDialog,
    },
    props: {
        'professions': Array,
    },
    data() {
        return {
            professionRules: [],
            indexProfession: -1,
            professionDialog: false,
            professionDialogModified: false,
            profession: "",
            professionsArray: [],
        };
    },
    computed: {
        ...mapGetters(["currentBussinesClub"]),
    },
    watch: {
        /* Profession dialog watch fields */
        'profession'(value) {
            this.professionDialogModified = true;
        },
    },
    created() {
        this.professionRequiredName = [
            v => !!v || '* ' + this.$t('message.professionRequiredName'),
            v => this.professions.indexOf(v) < 0 || '* ' + this.$t('message.professionDuplicated')
        ];
    },
    methods: {
        /**
         * Open the profession dialog
         */
        openProfessionDialog() {
            this.profession = "";
            this.$refs.profession.doReset()
            this.professionDialogModified = false;
            this.professionDialog = true
        },

        /**
         * Discard the changes into the profession dialog
         */
        discardProfession() {
            this.$refs.discardProfession.close();
            this.professionDialog = false
        },

        /**
         * Close the active certification dialog
         */
        closeProfession() {
            if (this.professionDialogModified) {
                this.$refs.discardProfession.openDialog();
            } else {
                this.professionDialog = false;
            }
        },

        /**
         * Save the profession dialog information
         */
        saveProfession() {
            if (!this.$refs.profession.doValidate()) {
                return;
            }

            this.addProfession(() => {
                this.profession = "";
                this.professionDialog = false
                this.professionDialog = false;
            });
        },

        /**
         * Show a popup to confirm the profession removal
         */
        askDeleteProfession(index) {
            this.indexProfession = index;
            this.$refs.deleteProfession.openDialog();
        },

        /**
         * Delete the current profession
         */
        deleteProfession() {
            if (this.indexProfession < 0 || this.indexProfession >= this.professions.length) {
                this.$refs.deleteProfession.close();
                return;
            }

            /* Update the service BD */
            this.$refs.deleteProfession.close();
            this.removeProfession(() => {
                this.indexProfession = -1;
            });
        },

        /**
         * Add a new profession
         */
        addProfession(cb) {
            const profession = {
                profession: this.profession,
            };
            this.dispatchAction("doProfileAddProfession", profession, cb);
        },

        /**
         * Remove a profession
         */
        removeProfession(cb) {
            const profession = {
                profession: this.professions[this.indexProfession],
            };
            this.dispatchAction("doProfileRemoveProfession", profession, cb);
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
