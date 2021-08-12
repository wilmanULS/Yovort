<template>
<v-flex v-if="viewReady" xs12 md6 lazy>
    <v-card class="colorBorderCards" color="transparent" :class="{'ml05': $vuetify.breakpoint.mdAndUp}">
        <v-card-title>
            <span class="titleCard darker-titles">{{$t('message.institutions')}}</span>
            <v-btn small :color="currentBussinesClub.theme.primary" fab dark absolute right @click="openInstitutionDialog(-1)">
                <v-icon>add</v-icon>
            </v-btn>
        </v-card-title>
        <v-card-text>
            <v-container class="no-padding">
                <v-layout column fill-height v-if="institutions.length<1">
                    <span class="noContent">{{$t('message.emptyInstitution')}}</span>
                </v-layout>
                <v-timeline dense clipped v-else>
                    <v-slide-x-transition group>
                        <v-timeline-item v-for="(item, index) in sortedInstitutions" :key="index" class="mb-3" :color="currentBussinesClub.theme.primary">
                            <template v-slot:icon>
                                <v-btn flat icon color="white" @click="openInstitutionDialog(index)">
                                    <v-icon small>edit</v-icon>
                                </v-btn>
                            </template>
                            <v-layout justify-space-between>
                                <v-flex xs12>
                                    <v-list-tile-content>
                                        <v-list-tile-title class="titleJob darker-titles" v-html="getObjectPath(item, 'title','')">
                                        </v-list-tile-title>
                                        <v-list-tile-sub-title class="darker-titles" v-html="getObjectPath(item, 'name','')"></v-list-tile-sub-title>
                                        <v-list-tile-sub-title class="darker-titles" v-html="getObjectPath(item, 'year.from','Undef') +' - '+getObjectPath(item, 'year.to','Undef')">
                                        </v-list-tile-sub-title>
                                        <v-list-tile-sub-title class="darker-titles" v-html="getCountry(getObjectPath(item, 'address.country','')) +' - '+getObjectPath(item, 'address.province','') +' - '+getObjectPath(item, 'address.city','')">
                                        </v-list-tile-sub-title>
                                    </v-list-tile-content>
                                </v-flex>
                            </v-layout>
                        </v-timeline-item>
                    </v-slide-x-transition>
                </v-timeline>
            </v-container>
        </v-card-text>
        <v-popup-dialog ref="institution" :model="institutionDialog" :title="indexInstitution<0?$t('message.addInstitutions'):$t('message.editInstitutions')" showRequired :showRemove="indexInstitution>=0" @onClose="closeInstitution"
            @onSave="saveInstitution" @onRemove="askDeleteInstitution">
            <v-layout row wrap>
                <v-flex xs12 md12>
                    <v-text-field ref='institutionText' :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.addInstitutionsLabel')+'*'" :rules="institutionName" v-model="institution.name"></v-text-field>
                    <v-text-field :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.titles')+'*'" :rules="institutionTitle" v-model="institution.title"></v-text-field>
                </v-flex>
                <v-divider></v-divider>
                <span class="pl-2 darker-titles"> {{$t('message.yearfromto')}} </span>
                <v-flex xs12 md12>
                    <v-layout row wrap>
                        <v-flex xs6 class="profile-flex-padding">
                            <v-select :rules="institutionFrom" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.from')+'*'" :no-data-text="$t('message.nodatafound')" @change="changeFocusYear(institution.year.from)"
                                browser-autocomplete append-icon="event" :items="yearsArray" v-model="institution.year.from"></v-select>
                        </v-flex>
                        <v-flex xs6 class="profile-flex-padding">
                            <v-select :rules="institutionTo" :no-data-text="$t('message.nodatafound')" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.to')+'*'" append-icon="event" :items="yearstoArray"
                                v-model="institution.year.to">
                            </v-select>
                        </v-flex>
                    </v-layout>
                </v-flex>
                <v-flex xs12 md12>
                    <v-city-address-picker :country="institution.address.country" :province="institution.address.province" :city="institution.address.city" @onValueChange="changeAddress"></v-city-address-picker>
                </v-flex>
            </v-layout>
        </v-popup-dialog>
        <v-confirm-dialog v-bind:titleToolbar="$t('message.discardChanges')" ref="discardInstitution" type="question" :heading="$t('message.changesWithoutSaving')" v-bind:message="$t('message.discardChangesLabelInstitution')"
            @onConfirm="discardInstitution()">
        </v-confirm-dialog>
        <v-confirm-dialog v-bind:titleToolbar="$t('message.deleteInstitution')" ref="deleteInstitution" type="question" :heading="$t('message.deleteInstitution')" v-bind:message="$t('message.deleteInstitutionLabel', {institution: institution.name})"
            @onConfirm="deleteInstitution()">
        </v-confirm-dialog>
    </v-card>
</v-flex>
</template>

<script>
import confirmDialog from "Components/ySocialHome/confirmDialog";
import {
    mapGetters
} from "vuex";
import Vue from "vue";
import CityAddressPicker from 'Components/CityAddressPicker/cityAddressPicker'

export default {
    components: {
        "v-confirm-dialog": confirmDialog,
        'v-city-address-picker': CityAddressPicker,
    },
    props: {
        'institutions': Array,
    },
    data() {
        return {
            viewReady: false,
            getObjectPath: Vue.getObjectPath,
            cityItems: [],
            /* Institutions form validation rules */
            institutionName: [],
            institutionTitle: [],
            institutionCountry: [],
            institutionFrom: [],
            institutionTo: [],
            /* Control years fields */
            yearsArray: [],
            yearstoArray: [],
            /* Institution data model */
            indexInstitution: -1,
            institutionDialog: false,
            institutionDialogModified: false,
            institution: {
                name: "",
                title: "",
                year: {
                    to: "",
                    from: ""
                },
                address: {
                    country: "",
                    province: "",
                    city: ""
                }
            },
        };
    },
    computed: {
        ...mapGetters(["countries", "provinces", "currentBussinesClub", "getCountry"]),
        sortedInstitutions: function() {
            return this.institutions.sort((a, b) => {
                if (a.year.from > b.year.from)
                    return -1;
                if (a.year.from < b.year.from)
                    return 1;
                if (a.year.to > b.year.to)
                    return -1;
                if (a.year.to < b.year.to)
                    return 1;
                if (a.name < b.name)
                    return -1;
                if (a.name > b.name)
                    return 1;
                return 0;
            });
        },
    },
    watch: {
        /* Institution dialog watch fields */
        'institution.name'(value) {
            this.institutionDialogModified = true;
        },
        'institution.title'(value) {
            this.institutionDialogModified = true;
        },
        'institution.year.to'(value) {
            this.institutionDialogModified = true;
        },
        'institution.year.from'(value) {
            this.institutionDialogModified = true;
        },
    },
    created() {
        this.institutionName = [v => !!v || '* ' + this.$t('message.instituteNameRequired')];
        this.institutionTitle = [v => !!v || '* ' + this.$t('message.instituteTitleRequired')];
        this.institutionCountry = [v => !!v || '* ' + this.$t('message.instituteCountry')];
        this.institutionTo = [(v) => !!v || '* ' + this.$t('message.instituteDateTo')];
        this.institutionFrom = [(v) => !!v || '* ' + this.$t('message.instituteDateFrom')];

        this.startYearPicker();
        this.$store.dispatch("doGetCountries")
            .finally(() => {
                this.viewReady = true;
            });
    },
    methods: {
        /**
         * Open the institution dialog
         */
        openInstitutionDialog(index) {
            this.indexInstitution = index;
            if (index < 0) {
                this.institution.name = null;
                this.institution.title = null;
                this.institution.year.to = '';
                this.institution.year.from = '';
                this.institution.address.country = null;
                this.institution.address.province = null;
                this.institution.address.city = null;
            } else {
                const tmpInstitution = this.institutions[index];
                this.institution.name = Vue.getObjectPath(tmpInstitution, 'name', null);
                this.institution.title = Vue.getObjectPath(tmpInstitution, 'title', null);
                this.institution.year.to = Vue.getObjectPath(tmpInstitution, 'year.to', '');
                this.institution.year.from = Vue.getObjectPath(tmpInstitution, 'year.from', '');
                this.changeFocusYear(this.institution.year.from);
                this.institution.address.country = Vue.getObjectPath(tmpInstitution, 'address.country', null);
                this.institution.address.province = Vue.getObjectPath(tmpInstitution, 'address.province', null);
                this.fetchProvinces(this.institution.address.country, () => {
                    this.institution.address.city = Vue.getObjectPath(tmpInstitution, 'address.city', null);
                    this.fetchCities(this.institution.address.province, () => {
                        this.institutionDialogModified = false;
                    });
                });
            }
            Vue.nextTick(() => {
                this.$refs.institution.doReset()
                this.institutionDialogModified = false;
                this.institutionDialog = true
            });
        },

        /**
         * Discard the changes into the institution dialog
         */
        discardInstitution() {
            this.$refs.discardInstitution.close();
            this.institutionDialog = false;
        },

        /**
         * Close the active institution dialog
         */
        closeInstitution() {
            if (this.institutionDialogModified) {
                this.$refs.discardInstitution.openDialog();
            } else {
                this.institutionDialog = false;
            }
        },

        /**
         * Save the certification dialog information
         */
        saveInstitution() {
            if (!this.$refs.institution.doValidate()) {
                return;
            }

            if (this.indexInstitution >= 0) {
                this.updateInstitution(() => {
                    this.institutionDialog = false;
                });
            } else {
                this.addInstitution(() => {
                    this.institutionDialog = false;
                });
            }
        },

        /**
         * Show a popup to confirm the institution removal
         */
        askDeleteInstitution() {
            if (this.indexInstitution < 0) {
                return;
            }

            this.$refs.deleteInstitution.openDialog();
        },

        /**
         * Delete the current institution
         */
        deleteInstitution() {
            if (this.indexInstitution < 0) {
                return;
            }

            /* Update into the Business Club */
            this.$refs.deleteInstitution.close();
            this.removeInstitution(() => {
                this.institutionDialog = false;
            });
        },

        changeAddress(payload) {
            this.institution.address.country = payload.country;
            this.institution.address.province = payload.province;
            this.institution.address.city = payload.city;
        },

        /**
         * Add a new institution
         */
        addInstitution(cb) {
            const institution = {
                name: this.institution.name,
                title: this.institution.title,
                to: this.institution.year.to,
                from: this.institution.year.from,
                country: this.institution.address.country,
                province: this.institution.address.province,
                city: this.institution.address.city,
            };
            this.dispatchAction("doProfileAddInstitution", institution, cb);
        },

        /**
         * Update an existant institution
         */
        updateInstitution(cb) {
            const institution = {
                institutionId: this.institutions[this.indexInstitution]._id,
                name: this.institution.name,
                title: this.institution.title,
                to: this.institution.year.to,
                from: this.institution.year.from,
                country: this.institution.address.country,
                province: this.institution.address.province,
                city: this.institution.address.city,
            };
            this.dispatchAction("doProfileUpdateInstitution", institution, cb);
        },

        /**
         * Remove a certification
         */
        removeInstitution(cb) {
            const institution = {
                institutions: [this.institutions[this.indexInstitution]._id],
            };
            this.dispatchAction("doProfileRemoveInstitution", institution, cb);
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

        /**
         * Fetch the provinces from the given country
         */
        fetchProvinces(country, cb) {
            this.$store.dispatch("doGetProvinces", {
                country: country,
                view: 'education'
            }).finally(() => {
                const indexProvince = this.provinces.findIndex(
                    prov => prov.name === this.institution.address.province
                );

                if (indexProvince < 0) {
                    this.institution.address.province = null;
                    this.institution.address.city = null;
                    this.cityItems = [];
                }
                if (cb) {
                    cb();
                }
            });
        },

        /**
         * Fetch the city information from the given province
         */
        fetchCities(province, cb) {
            const indexProvince = this.provinces.findIndex(
                prov => prov.name == province
            );

            if (province && indexProvince >= 0) {
                this.cityItems = this.provinces[indexProvince].cities;
                const indexCity = this.cityItems.findIndex(
                    city => city.name === this.institution.address.city
                );
                if (indexCity < 0) {
                    this.institution.address.city = null;

                }
            } else {
                this.institution.address.city = null;
                this.cityItems = [];
            }
            if (cb) {
                cb();
            }
        },

        /**
         * Initialize year picker year data
         */
        startYearPicker() {
            const end = new Date().getFullYear();
            for (let year = end; year >= 1950; year--) {
                this.yearsArray.push(year);
            }
        },

        /**
         * Update to years
         */
        changeFocusYear(from) {
            let end = new Date().getFullYear();
            this.yearstoArray = [];
            if (from != "") {
                for (let index = end; index >= from; index--) {
                    this.yearstoArray.push(index);
                }
                if (from > this.institution.year.to) {
                    this.institution.year.to = "";
                }
            } else {
                this.yearstoArray = [];
                this.institution.year.to = "";
            }
        },

        /**
         * Change country selector
         */
        changeCountry(value) {
            this.institutionDialogModified = true;
            this.fetchProvinces(this.institution.address.country);
        },

        /**
         * Change province selector
         */
        changeProvince(value) {
            this.institutionDialogModified = true;
            this.fetchCities(this.institution.address.province);
        },

        /**
         * Change city selector
         */
        changeCity(value) {
            this.institutionDialogModified = true;
        },
    }
};
</script>
