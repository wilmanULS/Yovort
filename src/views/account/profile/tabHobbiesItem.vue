<template>
<v-flex xs12 md6 lazy>
    <v-card :class="`colorBorderCards `+ ($vuetify.breakpoint.mdAndUp? className:'')" color="transparent">
        <v-card-title>
            <span class="titleCard darker-titles">{{$t('message.favorite' + type)}}</span>
            <v-btn small :color="currentBussinesClub.theme.primary" fab dark absolute right @click="openDialog()">
                <v-icon>add</v-icon>
            </v-btn>
        </v-card-title>
        <v-layout row align-center justify-end></v-layout>
        <v-card-text>
            <v-container class="no-padding">
                <v-list class="transparent" three-line>
                    <v-container ma-0 pa-0>
                        <v-layout justify-center column fill-height v-if="hobbyData.length<1">
                            <span class="noContent">{{$t('message.empty' + type)}}</span>
                        </v-layout>
                        <v-layout row wrap v-else>
                            <div v-for="(hobby, index) in hobbyData" :key="index">
                                <v-chip style="background: white!important;" outline :color="currentBussinesClub.theme.primary" :ref="'chip'+index" close @input="deleteDialog(index, hobby)"><span class="darker-titles">{{hobby}}</span></v-chip>
                            </div>
                        </v-layout>
                    </v-container>
                </v-list>
            </v-container>
        </v-card-text>
        <v-popup-dialog :model="addDialog" :title="$t('message.add' + type)" showRequired @onClose="closeDialogHobby" @onSave="addNewHobby">
            <v-flex xs12 md12>
                <v-text-field hide-details :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.write' + type)+'*'" v-model="newHobby"></v-text-field>
            </v-flex>
        </v-popup-dialog>
        <v-confirm-dialog v-bind:titleToolbar="$t('message.delete' + type)" ref="confirmDelete" type="question" :heading="$t('message.delete' + type)"
            v-bind:message="$t('message.deleteQuestion'+type,{hobby: indexHobby < hobbyData.length?hobbyData[indexHobby]: 'none'})" @onConfirm="removeHobbies()">
        </v-confirm-dialog>
        <v-confirm-dialog v-bind:titleToolbar="$t('message.discardChanges')" ref="confirmDiscard" type="question" :heading="$t('message.discardChanges')" v-bind:message="$t('message.discardChangesLabel')" @onConfirm="discardHobby()">
        </v-confirm-dialog>
    </v-card>
</v-flex>
</template>
<script>
import confirmDialog from "Components/ySocialHome/confirmDialog";

import {
    mapGetters
} from "vuex";
export default {
    props: {
        'type': String,
        'field': String,
        'className': String,
        'hobbyData': Array,
    },
    computed: {
        ...mapGetters(["currentBussinesClub"]),
    },
    data() {
        return {
            addDialog: false,
            activeDark: false,
            disabledButon: true,
            newHobby: "",
            indexHobby: -1,
        };
    },
    components: {
        "v-confirm-dialog": confirmDialog
    },
    methods: {
        openDialog() {
            this.newHobby = "";
            this.addDialog = true;
        },
        deleteDialog(index) {
            this.indexHobby = index;
            this.$refs.confirmDelete.openDialog();
        },
        removeHobbies() {
            const hobby = this.hobbyData[this.indexHobby];
            this.$refs.confirmDelete.close();
            this.indexHobby = -1;
            this.removeHobby(hobby, () => {
                this.newHobby = "";
                this.addDialog = false;
            });
        },
        needDiscard() {
            if (this.newHobby !== "") {
                return true;
            } else {
                return false;
            }
        },
        closeDialogHobby() {
            if (this.needDiscard()) {
                this.$refs.confirmDiscard.openDialog()
            } else {
                this.addDialog = false
            }
        },
        discardHobby() {
            this.$refs.confirmDiscard.close()
            this.addDialog = false
        },

        mouseOver: function() {
            this.active = !false;
        },

        addNewHobby() {
            const indexHobby = this.hobbyData.findIndex(
                hobby => hobby.toLowerCase() == this.newHobby.toLowerCase()
            );
            if (!/\S/.test(this.newHobby)) {
                this.$store.dispatch("doNotification", {
                    msg: this.$t("message.messageData" + this.type),
                    type: 'warning'
                });
            } else {
                if (this.newHobby != "" && indexHobby < 0) {
                    this.addHobby(this.newHobby, () => {
                        this.newHobby = "";
                        this.addDialog = false;
                    });
                } else {
                    this.$store.dispatch("doNotification", {
                        msg: this.$t("message.duplicatedData", {
                            hobby: this.newHobby
                        }),
                        type: 'warning'
                    });
                }
            }
        },

        /**
         * Add a new profession
         */
        addHobby(hobby, cb) {
            const hobbyObj = {
                field: this.field,
                hobby: hobby,
            };
            this.dispatchAction("doProfileAddHobby", hobbyObj, cb);
        },

        /**
         * Remove a hobby
         */
        removeHobby(hobby, cb) {
            const hobbyObj = {
                field: this.field,
                hobby: hobby,
            };
            this.dispatchAction("doProfileRemoveHobby", hobbyObj, cb);
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
