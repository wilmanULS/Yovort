<template>
<v-layout row wrap>
    <v-flex xs12 md12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-autocomplete class="border-bottom" :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" ref="country" :no-data-text="$t('message.nodatafound')" :items="countries" item-text="name"
            item-value="countryId" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.countryResidence')" :loading="countryLoading" hide-details @change="changeCountry(intCountry)" v-model="intCountry"
            style="overflow: hidden;white-space: nowrap;">
            <template slot="selection" slot-scope="data" style="overflow: hidden;white-space: nowrap;">
                <v-flex class='ml-1'>
                    <span style="overflow: hidden;white-space: nowrap;">
                        &nbsp;&nbsp;<flag :iso="data.item.flag" /> {{ data.item.name }}</span>
                </v-flex>
            </template>
            <template slot="item" slot-scope="data">
                <v-list-tile-content>
                    <v-list-tile-title>
                        <span style="overflow: hidden;white-space: nowrap;">
                            <flag :iso="data.item.flag" /> {{data.item.name}}</span>
                    </v-list-tile-title>
                </v-list-tile-content>
            </template>
        </v-autocomplete>
    </v-flex>
    <v-flex xs12 md12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-autocomplete class="border-bottom" :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }"  @change="changeProvince(intProvince)" :no-data-text="$t('message.nodatafound')" ref="province"
            :items="provinces" item-text="name" item-value="name" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.province')" :loading="provinceLoading" :disabled="readOnlyProvince" hide-details attach v-model="intProvince">
        </v-autocomplete>
    </v-flex>
    <v-flex xs12 md12  :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
        <v-autocomplete class="border-bottom" :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" @change="changeCity(intCity)" :no-data-text="$t('message.nodatafound')" ref="city" :items="cities" item-text="name" item-value="name"
            :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.city')" :loading="cityLoading" :disabled="readOnlyCity" hide-details attach v-model="intCity"></v-autocomplete>
    </v-flex>
</v-layout>
</template>
<script>
import Vue from 'vue';
import {
    mapGetters
} from "vuex";
export default {
    props: {
        'country': String,
        'province': String,
        'city': String,
    },
    watch: {
        country(value) {
            this.intCountry = value;
            if (!this.readOnlyProvince && !this.readOnlyCity) {
                this.intCity = "";
                this.intProvince = "";
                this.readOnlyCity = true;
            }
            if (this.intCountry != '' && this.intCountry != null) {
                this.setProvinces(this.intCountry);
            }
        },
        province(value) {
            this.intProvince = value;
            if (this.intProvince != '' && this.intProvince != null) {
                this.getCity(this.intProvince);
            }
        },
        city(value) {
            this.intCity = value;
        },
    },
    created() {
        Vue.nextTick(() => {
            /* Load country information */
            this.countryLoading = true;
            this.$store.dispatch("doGetCountries")
                .finally(() => {
                    this.countryLoading = false;
                });
        });
    },
    data() {
        return {
            intCountry: null,
            intProvince: null,
            intCity: null,
            cities: [],
            readOnlyProvince: true,
            readOnlyCity: true,
            countryLoading: false,
            provinceLoading: false,
            cityLoading: false,
        };
    },
    computed: {
        ...mapGetters([
            "countries",
            "provinces",
            "currentBussinesClub",
            "selectedLocale"
        ]),
    },

    methods: {
        changeValues(country,province,city){
            this.intCountry = country;
            this.intProvince = province;
            this.intCity = city;
        },

        clearFields() {
            this.$refs.country.reset()
            this.$refs.province.reset()
            this.$refs.city.reset()
            this.intCountry = null;
            this.intProvince = null;
            this.intCity = null;
        },

        emitEvent() {
            this.$emit('onValueChange', {
                country: this.intCountry,
                province: this.intProvince,
                city: this.intCity
            });
        },

        /**
         * Event triggered on country change
         */
        changeCountry(country) {
            this.$refs.province.reset()
            this.$refs.city.reset()
            this.readOnlyProvince = true;
            this.readOnlyCity = true;
            this.intProvince = null;
            this.intCity = null;
            this.emitEvent();
            this.setProvinces(country);
        },

        /**
         * Event triggered on province change
         */
        changeProvince(province) {
            this.getCity(province);
            this.intCity = null;
            this.emitEvent();
        },

        /**
         * Event triggered on city change
         */
        changeCity(city) {
            this.emitEvent();
        },

        /**
         * Load all provinces from the given country
         */
        setProvinces(country) {
            this.provinceLoading = true;
            this.$store.dispatch("doGetProvinces", {
                country: country,
            }).then(res => {
                this.getCity(this.province)
            }).finally(() => {
                this.provinceLoading = false;
                this.readOnlyProvince = false
            });
        },

        /**
         * Load all cities from the given province
         */
        getCity(province) {
            this.cityLoading = true;
            const indexProvince = this.provinces.findIndex(
                prov => prov.name == province
            );
            if (indexProvince != -1) {
                this.readOnlyCity = false
                this.cities = this.provinces[indexProvince].cities;
            }
            this.cityLoading = false;
        },
    }
};
</script>
