<template>
<v-flex xs12 md6 lazy>
    <v-card class="colorBorderCards" color="transparent" :class="{'ml05': $vuetify.breakpoint.mdAndUp}">
        <v-card-title>
            <span class="titleCard darker-titles">{{$t('message.skills')}}</span>
            <v-btn small :color="currentBussinesClub.theme.primary" fab dark absolute right @click="openSkillDialog()">
                <v-icon>add</v-icon>
            </v-btn>
        </v-card-title>
        <v-layout row align-center justify-end>
        </v-layout>
        <v-card-text>
            <v-container class="no-padding">
                <v-layout justify-center column fill-height v-if="skills.length===0">
                    <span class="noContent">{{$t('message.emptySkills')}}</span>
                </v-layout>
                <v-layout class="mb-2 xs12" row wrap v-for="(item, index) in skills" :key="item._id">
                    <v-flex md11>
                        <span class="textHeaderSkill darker-titles pb-2">{{item.name}}</span>
                        <div class="line" :style="`width: ${item.value}%;`">
                            <div class="skills-value">{{item.value}}%</div>
                        </div>
                    </v-flex>
                    <v-flex md1>
                        <v-btn flat icon :color="currentBussinesClub.theme.primary" @click="editSkill(item, index)">
                            <v-icon small>edit</v-icon>
                        </v-btn>
                    </v-flex>
                </v-layout>
            </v-container>
        </v-card-text>
        <v-popup-dialog ref="formSkill" :model="skillDialog" :title="skillIndex<0?$t('message.addSkill'):$t('message.editSkill')" showRequired :showRemove="skillIndex>=0" @onClose="closeAdd" @onSave="saveSkill" @onRemove="openDeleteDialog">
            <v-layout row wrap>
                <v-flex xs12>
                    <v-text-field ref="skillName" :rules="skillNameRules" required :color="currentBussinesClub.theme.primary" :label="$t('message.skill')+' *'" v-model="skillName"></v-text-field>
                </v-flex>
                <v-flex xs12 class="profile-flex-padding">
                    <v-slider :color="currentBussinesClub.theme.primary" v-model="skillValue" thumb-label="always"></v-slider>
                </v-flex>
            </v-layout>
        </v-popup-dialog>
        <v-confirm-dialog :titleToolbar="$t('message.deleteSkill')" ref="skillDeleteDialog" type="question" :heading="$t('message.deleteSkill')" :message="$t('message.confirmMessageDeleteSkill')" @onConfirm="deleteItemSkill"></v-confirm-dialog>
        <v-confirm-dialog v-bind:titleToolbar="$t('message.discardChanges')" ref="discardSkill" type="question" :heading="$t('message.changesWithoutSaving')" v-bind:message="$t('message.discardChangesLabel')" @onConfirm="closeDiscardSkill">
        </v-confirm-dialog>
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
        "v-confirm-dialog": confirmDialog,
    },
    data() {
        return {
            skillIndex: -1,
            skillDialog: false,
            skillName: '',
            skillValue: 0,
            skillNameRules: [],
            getObjectPath: Vue.getObjectPath,
            skills: [],
            settings: {
                maxScrollbarLength: 160
            }
        };
    },
    computed: {
        ...mapGetters(["userProfile", "currentBussinesClub"]),
        userProfile: {
            get() {
                if (this.$store.getters.userProfile != '')
                    return JSON.parse(this.$store.getters.userProfile);
            }
        },
    },
    watch: {
        userProfile(val) {
            Vue.nextTick(() => {
                this.skills = this.userProfile.skills || [];
            });
        },
    },
    mounted() {
        this.$nextTick(() => {
            this.skills = this.userProfile.skills || [];
        });
    },
    created() {
        this.skillNameRules = [(v) => !!v || this.$t('message.skillNameRule')];
    },
    methods: {
        editSkill(item, index) {
            this.skillIndex = index;
            this.skillName = Vue.getObjectPath(item, 'name', '');
            this.skillValue = Vue.getObjectPath(item, 'value', 0);
            this.$refs.skillName.resetValidation();
            this.skillDialog = true
        },

        closeDiscardSkill() {
            this.$refs.discardSkill.close()
            this.skillDialog = false;
        },

        discardSkill() {
            if (this.skillName !== "" || this.skillValue !== 0) {
                return true;
            } else {
                return false;
            }
        },

        /**
         * Show the add skill dialog
         */
        openSkillDialog() {
            this.skillIndex = -1;
            this.skillName = "";
            this.skillValue = 0;
            this.$refs.formSkill.doReset()
            this.$refs.skillName.resetValidation();
            this.skillDialog = true
        },

        closeAdd() {
            if (this.discardSkill()) {
                this.$refs.discardSkill.openDialog()
                this.$refs.skillName.resetValidation();
            } else {
                this.skillDialog = false;
            }
        },

        saveSkill() {
            if (this.$refs.formSkill.doValidate()) {
                if (this.skillIndex >= 0) {
                    this.updateSkill(() => {
                        this.skillDialog = false;
                    });
                } else {
                    this.addSkill(() => {
                        this.skillDialog = false;
                    });
                }
            }
        },

        deleteItemSkill() {
            /* Remove the selected skill */
            this.$refs.skillDeleteDialog.close();
            this.removeSkill(() => {
                this.skillIndex = -1;
                this.skillDialog = false;
            });
        },

        /**
         * Add a new skill
         */
        addSkill(cb) {
            const skill = {
                name: this.skillName,
                value: this.skillValue,
            };
            this.dispatchAction("doProfileAddSkill", skill, cb);
        },

        /**
         * Update an existant skill
         */
        updateSkill(cb) {
            const skill = {
                skillId: this.skills[this.skillIndex]._id,
                name: this.skillName,
                value: this.skillValue,
            };
            this.dispatchAction("doProfileUpdateSkill", skill, cb);
        },

        /**
         * Remove an skill
         */
        removeSkill(cb) {
            const skill = {
                skills: [this.skills[this.skillIndex]._id],
            };
            this.dispatchAction("doProfileRemoveSkill", skill, cb);
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

        openDeleteDialog() {
            this.$refs.skillDeleteDialog.openDialog();
        },
    }
};
</script>
<style scoped>
.line {
    background: var(--primary);
    padding-top: 5px;
    padding-bottom: 5px;
    position: relative;
    height: 1px !important;
    border-radius: 10px;
}

.line:after {
    content: "";
    width: 18px;
    height: 18px;
    background-color: #fff;
    border: 3px solid var(--primary);
    border-radius: 8px;
    position: absolute;
    top: -5px;
}

.line:after {
    right: 0;
}

.skills-value {
    position: absolute;
    top: -30px;
    color: var(--primary);
    right: 0;
    font-size: 14px;
    font-weight: 600;
}


.textHeaderSkill {
    margin: 0px 0px 10px;
    font-size: 14px;
}
</style>
