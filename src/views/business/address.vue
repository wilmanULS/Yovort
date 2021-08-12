<template>
<v-form v-model="valid" ref="form" autocomplete="off">
	<v-flex>
		<v-layout row wrap style="heigth: 100%!important;">
			<v-flex xs12 md6>
				<v-flex xs12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
					<v-autocomplete :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" ref="country" :no-data-text="$t('message.nodatafound')" :items="countries" item-text="name" item-value="countryId"
					 :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.country')+' (*)'" hide-details attach :loading="loadingCountries" @change="changeCountry(intCountry)" v-model="intCountry" class="border-bottom" required
					 browser-autocomplete="off" :rules="nameRules">
						<template slot="selection" slot-scope="data">
							<span>
								<flag :iso="data.item.flag" />
								{{ data.item.name }}
							</span>
						</template>
						<template slot="item" slot-scope="data">
							<v-list-tile-content>
								<v-list-tile-title>
									<span style="overflow: hidden;white-space: nowrap;">
										<flag :iso="data.item.flag" />
										{{data.item.name}}
									</span>
								</v-list-tile-title>
							</v-list-tile-content>
						</template>
					</v-autocomplete>
				</v-flex>
				<v-flex xs12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
					<v-autocomplete class="border-bottom" :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" @change="changeProvince(intProvince)" :no-data-text="$t('message.nodatafound')" ref="province"
					 :items="provinces" item-text="name" item-value="name" browser-autocomplete="off" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.province')+' (*)'" :loading="provinceLoading" :disabled="readOnlyProvince"
					 hide-details attach v-model="intProvince" required :rules="nameRules"></v-autocomplete>
				</v-flex>
				<v-flex xs12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
					<v-autocomplete class="border-bottom" :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" :no-data-text="$t('message.nodatafound')" ref="city" :items="cities" item-text="name" item-value="name"
					 browser-autocomplete="off" :color="currentBussinesClub.theme.primary" :label="$t('message.city')+' (*)'" :loading="cityLoading" :disabled="readOnlyCity" hide-details attach v-model="intCity" @change="changeCity(intCity)" required
					 :rules="nameRules"></v-autocomplete>
				</v-flex>
				<v-flex xs12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
					<v-text-field :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" clearable hide-details browser-autocomplete="off" :color="currentBussinesClub.theme.primary"
					 v-bind:label="$t('message.vBusinessAddressPostal')+' (*)'" ref="postalAddress" v-model="fullAddress" required :rules="nameRules" @change="doGeoPosition(fullAddress)"></v-text-field>
				</v-flex>
				<v-flex xs12 :class="{'padding-step-wizard': $vuetify.breakpoint.mdAndUp}">
					<v-text-field :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" hide-details clearable required browser-autocomplete="off" :color="currentBussinesClub.theme.primary"
					 v-bind:label="$t('message.vBusinessCodePostal')+' (*)'" v-model="postalCode" ref="postalCode" :rules="nameRules"></v-text-field>
				</v-flex>
				<v-flex text-xs-left text-md-left pl-1 xs12>
					<span style="font-weight:bold!important" class="requeridTitles">* {{$t('message.requiredFields')}}</span>
				</v-flex>
			</v-flex>

			<v-flex xs12 md6>
				<v-flex xs12>
					<span :class="{'vb-ml': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" class="grey--text">
						<strong>{{$t('message.vBusinessWhereIs')}}</strong>
					</span>
				</v-flex>
				<v-flex xs12>
					<v-yovirt-map :class="{'vb-ml': $vuetify.breakpoint.mdAndUp,  'mb-1':$vuetify.breakpoint.smAndDown }" ref="map" style="border:1px solid var(--primary);margin-right:20px!important"></v-yovirt-map>
				</v-flex>
				<v-flex text-xs-left text-md-left pl-1 xs12></v-flex>
			</v-flex>
		</v-layout>
		<v-flex xs12 pt-3>
			<span class="grey--text">
				<strong>{{$t('message.vBusinessStepAddressNote')}}</strong>
			</span>
		</v-flex>
		<v-flex xs12 pt-3>
			<v-btn style="margin-left:0px" :color="currentBussinesClub.theme.primary" dark @click="saveMainAddress()">{{$t('message.vBusinessStepBusinessContinue')}}</v-btn>
			<v-btn @click="goBack(2)" flat :color="currentBussinesClub.theme.secondary">{{$t('message.vBusinessStepBusinessBack')}}</v-btn>
		</v-flex>
	</v-flex>
</v-form>
</template>
<script>

  import Vue from 'vue'
  import { mapGetters } from 'vuex'
  import { EventBus } from '../../eventBus.js'
  import YovirtMap from '../../components/Map/yovirtMap'
  import { GeoSearchControl, OpenStreetMapProvider } from 'leaflet-geosearch'
  import CityAddressPicker from 'Components/CityAddressPicker/cityAddressPicker'
  const provider = new OpenStreetMapProvider()
  export default {
    components: {
      'v-yovirt-map': YovirtMap,
      'v-city-address-picker': CityAddressPicker
    },
    props: {
      defaultCountry: String,
      currentBusinessId: String,
      stepData: [Array]
    },
    data() {
      return {
        /* Vars for validations*/
        readOnlyProvince: true,
        readOnlyCity: true,
        cityLoading: false,
        loadingCountries: false,
        provinceLoading: false,
        valid: false,
        nameRules: [],
        required: '',
        /* Data use as fields for v-model */
        _id: null,
        intCountry: null,
        intProvince: null,
        intCity: null,
        fullAddress: '',
        postalCode: '',
        latitude: 0,
        longitude: 0,
        mainAddress: true,
        /* To storage cities list*/
        cities: [],
        key:1
      }
    },
    watch: {
      intCountry(value) {
        this.key++;
        this.intCountry = value
        if (!this.readOnlyProvince && !this.readOnlyCity) {
          this.intCity = ''
          this.intProvince = ''
          this.readOnlyCity = true
        }
        if (this.intCountry != '' && this.intCountry != null) {
          this.setProvinces(this.intCountry)
        }
      },
      intProvince(value) {
        this.key++;
        this.intProvince = value
        if (this.intProvince != '' && this.intProvince != null) {
          this.getCity(this.intProvince)
        }
      },
      intCity(value) {
        this.key++;
        this.intCity = value
      }
    },
    computed: {
      ...mapGetters(['userProfile', 'currentBussinesClub', 'countries', 'provinces'])
    },
    created() {
      //this.reloadFields()
      EventBus.$on('clearWizard', () => {
        this.clearData()
      })
      EventBus.$on('addressMapSelected', payload => {
        this.doGeoReverse(payload)
      })
      this.required = this.$t('message.required')
      this.nameRules = [v => !!v || this.required]
      /* Load catalogs lists for this view */
      Vue.nextTick(() => {
        this.key++;
        this.loadingCountries = true
        this.$store.dispatch('doGetCountries').finally(() => {
          this.loadingCountries = false
        })
      })
    },
    mounted() {
      this.reloadFields()
    },
    methods: {
      doGeoPosition(val) {        
      //  let find = 
        provider.search({ query: val }).then(result => {
          this.latitude = parseFloat(result[0].y)
          this.longitude = parseFloat(result[0].x)
          EventBus.$emit('setCoordenatesMap', { latitude: parseFloat(result[0].y), longitude: parseFloat(result[0].x) })
        })
      },
      doGeoReverse(payload) {
        this.key++;
        this.latitude = payload.latlng.lat
        this.longitude = payload.latlng.lng
        let address = Vue.getObjectPath(payload, 'address.Address', '')
        if (address == '') {
          address = Vue.getObjectPath(payload, 'address.Match_addr', '')
        }
        this.fullAddress = address
        let province = Vue.getObjectPath(payload, 'address.Region', '').toLowerCase()
        let city = Vue.getObjectPath(payload, 'address.City', '').toLowerCase()
        let country = this.countries.filter(c => c.iso == Vue.getObjectPath(payload, 'address.CountryCode', ''))
        if (country && country.length > 0) {
          this.intCountry = country[0].countryId
          let provinceFind = this.provinces.filter(p => p.name.toLowerCase() == province)
          if (provinceFind && provinceFind.length > 0) {
            this.intProvince = provinceFind[0].name
            let cityFind = this.cities.filter(c => c.name.toLowerCase() == city)
            if (cityFind && cityFind.length > 0) {
              this.intCity = cityFind[0].name
            }
          }
        }
      },
      reloadFields() {
        this.fullAddress = Vue.getObjectPath(this.stepData, '0.address.fullAddress', '')
        this.intCountry = Vue.getObjectPath(this.stepData, '0.address.country', '')
        this.intProvince = Vue.getObjectPath(this.stepData, '0.address.province', '')
        this.intCity = Vue.getObjectPath(this.stepData,'0.address.city', Vue.getObjectPath(this.stepData, 'identity.operationCountry', ''))
        this.postalCode = Vue.getObjectPath(this.stepData, '0.address.postalCode', '')
        this.mainAddress = Vue.getObjectPath(this.stepData, '0.address.mainAddress', false)
        this.latitude = Vue.getObjectPath(this.stepData, '0.address.location.latitude', '0')
        this.longitude = Vue.getObjectPath(this.stepData, '0.address.location.longitude', '0')
        EventBus.$emit('setCoordenatesMap', {
          latitude: this.latitude,
          longitude: this.longitude
        })
      },
      clearData() {
       /*  this.$refs.country.reset()
        this.$refs.province.reset()
        this.$refs.city.reset()
        this.postalAddress = ''
        this.postalCode = '' */
      },
      goBack(data) {
        this.$emit('onGoBack', data)
      },
      saveMainAddress() {
        //console.log("this is addres", this.stepData);
        if (this.$refs.form.validate()) {
          this.$root.$children[0].$emit('showLoading', 'Guardando información del negocio')
          let addressData = {
            type: 'address',
            _id : Vue.getObjectPath(this.stepData, '0._id', null),
            address: {
              country: this.intCountry,
              province: this.intProvince,
              city: this.intCity,
              fullAddress: this.fullAddress,
              postalCode: this.postalCode,
              location: {
                latitude: this.latitude,
                longitude: this.longitude
              }
            },
          }
          this.$store
            .dispatch('doAddBusinessAddress', {
              businessId: this.currentBusinessId,
              address: addressData,
              
            })
            .then(res => {
              this.$root.$children[0].$emit('hideLoading')
              this.$emit('onContinue')
            })
            .catch(err => {
              this.$store.dispatch('doNotification', {
                msg: this.$t('message.couldNotAddBusinessAddress'),
                type: 'error'
              })
            })
        } else {
          this.$store.dispatch('doNotification', {
            msg: this.$t('message.couldNotAddBusinessAddress'),
            type: 'error'
          })
        }
      },
      changeCountry(country) {
        this.$refs.province.reset()
        this.$refs.city.reset()
        this.readOnlyProvince = true
        this.readOnlyCity = true
        this.intProvince = null
        this.intCity = null
        this.setProvinces(country)
      },
      /* Load all provinces from the given country */
      setProvinces(country) {
        this.provinceLoading = true
        this.$store
          .dispatch('doGetProvinces', {
            country: country
          })
          .then(res => {
            this.getCity(this.intProvince)
          })
          .finally(() => {
            this.provinceLoading = false
            this.readOnlyProvince = false
          })
      },
      /* Event triggered on province change */
      changeProvince(province) {
        this.getCity(province)
        this.intCity = null
      },
      /* Event triggered on city change */
      changeCity(city) {
        this.intCity = city
      },
      /* Load all cities from the given province */
      getCity(province) {
        this.cityLoading = true
        const indexProvince = this.provinces.findIndex(prov => prov.name == province)
        if (indexProvince != -1) {
          this.readOnlyCity = false
          this.cities = this.provinces[indexProvince].cities
        }
        this.cityLoading = false
      }
    }
  }
</script>
<style>
.mrAddress {
	margin-right: 1rem !important;
}

.mlAddress {
	margin-left: 1rem !important;
	margin-top: 0px !important;
}
</style>
