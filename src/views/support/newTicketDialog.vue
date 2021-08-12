<template>
<v-popup-dialog ref="createForm" :model="open" :title="$t('message.newTicket')+'*'" showRequired @onSave="initCreateTicket()" @onClose="closeDialog()">
    <v-layout row wrap>
        <v-flex xs12>
            <v-text-field :color="currentBussinesClub.theme.primary" v-model="newTicket.topic" :label="$t('message.topic')+'*'" :rules="requiredField"></v-text-field>
        </v-flex>
        <v-flex xs12>
            <v-textarea :color="currentBussinesClub.theme.primary" :label="$t('message.description')+'*'" v-model="newTicket.description" auto-grow rows="1" :rules="requiredField" counter maxlength="300"></v-textarea>
        </v-flex>
        <v-flex xs12 md6>
            <v-select :no-data-text="$t('message.nodatafound')" :color="currentBussinesClub.theme.primary" v-model="newTicket.category" :items="categoriesTickets" item-text="name" item-value="_id" :label="$t('message.category')+'*'"
                :rules="requiredFieldElements"></v-select>
        </v-flex>
        <v-flex xs12 md6>
            <v-select :no-data-text="$t('message.nodatafound')" :color="currentBussinesClub.theme.primary" v-model="newTicket.priority" :items="priorities" item-text="name" item-value="value" :label="$t('message.priority')+'*'"
                :rules="requiredFieldElements"></v-select>
        </v-flex>
        <v-flex xs12>
            <v-combobox :color="currentBussinesClub.theme.primary" :label="$t('message.tags')" v-model="newTicket.tags" Tags multiple>
                <template slot="selection" slot-scope="data">
                    <v-chip :color="currentBussinesClub.theme.secondary" outline :selected="data.selected" close @input="removeTag(data.item)">
                        <strong>{{ data.item }}</strong>&nbsp;
                    </v-chip>
                </template>
            </v-combobox>
        </v-flex>
        <v-flex xs12>
            <label class="darker-titles">{{$t('message.addAttachment')}}</label>
            <v-upload-button refName="uploadNewTicket" ref="upload"></v-upload-button>
        </v-flex>
    </v-layout>
    <v-confirm-dialog v-bind:titleToolbar="'Cambios sin guardar'" ref="closeDialog" type="question" heading="Cambios sin guardar" v-bind:message="'¿Está seguro que desea salir?'" @onConfirm="open = false"></v-confirm-dialog>
</v-popup-dialog>
</template>
<script>
import confirmDialog from "Components/ySocialHome/confirmDialog";
import Vue from "vue";
import {
    mapGetters
} from "vuex";
export default {
    mounted() {
        Vue.nextTick(() => {
            this.$watch(
                () => {
                    return this.$refs.upload.$refs.uploadNewTicket.uploaded;
                },
                val => {
                    if (val) {
                        try {
                            var noError = true;
                            var attachements = [];
                            var files = this.$refs.upload.$refs.uploadNewTicket.files;
                            if (files.length > 0) {
                                try {
                                    for (var i = 0; i < files.length; i++) {
                                        var contentType = files[i].response.data.mimetype;
                                        var url = files[i].response.data.path.replace(/ /g, "%20");
                                        var fileName = files[i].response.data.originalName;
                                        var size = files[i].response.data.size;
                                        this.additionalAttachments.push({
                                            contentType: contentType,
                                            url: {
                                                url: url,
                                                filename: fileName,
                                                size: size
                                            }
                                        });
                                    }
                                } catch (error) {
                                    noError = false;
                                    this.$refs.upload.$refs.uploadNewTicket.emitInput();
                                    this.$refs.upload.$refs.uploadNewTicket.clear();
                                    this.$root.$children[0].$emit("hideLoading");
                                } finally {
                                    if (noError) {
                                        this.dispatchNewTicket();
                                    }
                                }
                            }
                        } catch (error) {}
                    }
                }
            );
        });
    },
    computed: {
        ...mapGetters(["userProfile", "currentBussinesClub", "categoriesTickets"])
    },
    data() {
        return {
            open: false,
            priorities: [{
                    name: "Baja",
                    value: "low"
                },

                {
                    name: "Normal",
                    value: "normal"
                },
                {
                    name: "Media",
                    value: "medium"
                },
                {
                    name: "Alta",
                    value: "high"
                }
            ],
            newTicket: {
                topic: "",
                category: "",
                description: "",
                priority: "",
                parent: "",
                type:"club",
                tags: []
            },
            unSavedChanges: false,
            requiredField: [v => !!v || "Campo requerido"],
            requiredFieldElements: [v => (v && v.length > 0) || "Campo requerido"],
            validForm: false,
            additionalAttachments: []
        };
    },
    methods: {
        openDialog() {
            this.readyForm = false;
            this.$store
                .dispatch("getCategoriesUser", {
                    type: "ticket",
                    parent: this.currentBussinesClub.clubId
                })
                .then(res => {
                    this.newTicket.parent = this.currentBussinesClub.clubId;
                    this.newTicket.topic = "";
                    this.newTicket.category = "";
                    this.newTicket.description = "";
                    this.newTicket.priority = "";
                    this.newTicket.tags = [];
                    this.$refs.createForm.$refs.popupDialog.reset();
                    this.$refs.upload.$refs.uploadNewTicket.clear();
                    this.additionalAttachments = [];
                    this.unSavedChanges = false;
                })
                .finally(res => {
                    this.open = true;
                });
        },
        closeDialog() {
            if (this.unSavedChanges) {
                this.$refs.closeDialog.openDialog();
            } else {
                this.open = false;
            }
        },
        initCreateTicket() {
            this.$root.$children[0].$emit(
                "showLoading",
                this.$t("message.creatingTicket")
            );
            if (!this.$refs.upload.$refs.uploadNewTicket.uploaded) {
                this.$refs.upload.$refs.uploadNewTicket.active = true;
            } else {
                this.dispatchNewTicket();
            }
        },
        dispatchNewTicket() {
            this.newTicket.attachment = this.additionalAttachments;
            this.$store.dispatch("createTicket", this.newTicket).then(res => {
                this.open = false;
                this.$root.$children[0].$emit("hideLoading");
            });
        },
        removeTag(item) {
            this.newTicket.tags.splice(this.newTicket.tags.indexOf(item), 1);
            this.newTicket.tags = [...this.newTicket.tags];
        }
    },
    watch: {
        newTicket: {
            handler: function(val, oldVal) {
                if (this.open) {
                    this.unSavedChanges = true;
                }
            },
            deep: true
        }
    },
    components: {
        "v-confirm-dialog": confirmDialog
    }
};
</script>
