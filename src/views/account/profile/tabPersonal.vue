<template>
<div lazy>
	<v-form onSubmit="return false;" ref="personalInfo" v-model="valid" lazy-validation>
		<v-card flat color="transparent">
			<v-layout row wrap class="mb-1">
				<v-flex xs12 md6 class="profile-flex-padding">
					<v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" clearable ref="names" @keyup="activeButton()" :rules="namesRules" :color="currentBussinesClub.theme.primary"
					 v-bind:label="$t('message.name')+' *'" hide-details required v-model="names">
					</v-text-field>
				</v-flex>
				<v-flex xs12 md6 class="profile-flex-padding">
					<v-text-field :class="{'ml05': $vuetify.breakpoint.mdAndUp}" clearable required ref="surnames" @keyup="activeButton()" :rules="namesRules" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.lastName')+' *'"
					 hide-details v-model="surnames">
					</v-text-field>
				</v-flex>
			</v-layout>
			<v-layout row wrap class="mb-1">
				<v-flex xs12 md6 class="profile-flex-padding">
					<v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" clearable :rules="identificationRules" ref="identification" @keyup="activeButton()" :color="currentBussinesClub.theme.primary"
					 v-bind:label="$t('message.identification') +' *'" hide-details v-model="identity">
					</v-text-field>
				</v-flex>
				<v-flex xs12 md6 class="profile-flex-padding">
					<v-text-field :class="{'ml05': $vuetify.breakpoint.mdAndUp}" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.email')+' *'" readonly hide-details v-model="email">
					</v-text-field>
				</v-flex>
			</v-layout>
			<v-layout row wrap class="mb-1">
				<v-flex xs12 md6 class="profile-flex-padding">
					<v-menu class="pt-0 pb-0" ref="birthdateMenu" v-model="birthdateMenu" :close-on-content-click="false" :nudge-right="40" :return-value.sync="birthdate" lazy transition="scale-transition" offset-y full-width min-width="290px">
						<template v-slot:activator="{ on }">
							<v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp , 'mb-1':$vuetify.breakpoint.smAndDown }" v-model="birthdate" ref="birthdate" readonly v-on="on" :color="currentBussinesClub.theme.primary"
							 v-bind:label="$t('message.birthdate')+' *'" append-icon="event" :rules="birthdateRules" hide-details></v-text-field>
						</template>
						<v-date-picker v-model="birthdate" ref="birthdatePicker" :max="calculateMax()" :color="currentBussinesClub.theme.primary" :locale="selectedLocale.locale" @change="save">
						</v-date-picker>
					</v-menu>
				</v-flex>
				<v-flex xs12 md6 class="profile-flex-padding">
					<v-layout row wrap>
						<v-flex xs12>
							<v-telephone-input :class="{'ml05': $vuetify.breakpoint.mdAndUp}" :phoneRules="phoneRules" ref="number" :disabledFetchingCountry="true" v-model="phone" :color="currentBussinesClub.theme.primary"
							 :placeholder="$t('message.phoneNumber')" hide-details :required="true" @onInput="onInput" :preferredCountries="['ec']">
							</v-telephone-input>
						</v-flex>
					</v-layout>
				</v-flex>
			</v-layout>
			<v-layout row wrap class="mb-1">
				<v-flex xs12 md6 class="profile-flex-padding">
					<v-autocomplete :class="{'mr05': $vuetify.breakpoint.mdAndUp , 'mb-1':$vuetify.breakpoint.smAndDown }" ref="birthCountry" @change="activeButton()" :no-data-text="$t('message.nodatafound')" :items="countries" item-text="name"
					 item-value="countryId" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.countryBirth')" :loading="countryLoading" hide-details v-model="countryBirth">
						<template slot="selection" slot-scope="data">
							<v-flex class='ml-1'>
								&nbsp;&nbsp;
								<flag :iso="data.item.flag" /> {{ data.item.name }}
							</v-flex>
						</template>
						<template slot="item" slot-scope="data">
							<v-list-tile-content>
								<v-list-tile-title>
									<flag :iso="data.item.flag" /> {{data.item.name}}</v-list-tile-title>
							</v-list-tile-content>
						</template>
					</v-autocomplete>
				</v-flex>
				<v-flex xs12 md6 class="profile-flex-padding">
					<v-select :class="{'ml05': $vuetify.breakpoint.mdAndUp}" :no-data-text="$t('message.nodatafound')" :items="jobGroups" item-text="name" item-value="code" @change="activeButton()" ref="ocupation"
					 :color="currentBussinesClub.theme.primary" :loading="ocupationLoading" v-bind:label="$t('message.occupation')" hide-details v-model="occupation">
					</v-select>
				</v-flex>
			</v-layout>
			<v-layout row wrap class="mb-1">
				<v-flex xs12 md6 class="profile-flex-padding">
					<v-select :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" ref="gender" @change="activeButton()" :no-data-text="$t('message.nodatafound')" :items="genders" item-text="name" item-value="code"
					 :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.gender')" hide-details :loading="genderLoading" v-model="gender">
					</v-select>
				</v-flex>
				<v-flex xs12 md6 class="profile-flex-padding">
					<v-select :class="{'ml05': $vuetify.breakpoint.mdAndUp}" ref="marital" @change="activeButton()" :no-data-text="$t('message.nodatafound')" :items="maritalStatus" item-text="name" item-value="code"
					 :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.civilStatus')" :loading="maritalStatusLoading" hide-details v-model="maritalState">
					</v-select>
				</v-flex>
			</v-layout>
			<v-layout row wrap class="mb-1">
				<v-city-address-picker :country="country" :province="province" :city="city" @onValueChange="changeAddress"></v-city-address-picker>
			</v-layout>
			<v-layout row wrap class="mb-1">
				<v-flex xs12 class="profile-flex-padding">
					<v-textarea ref="description" @keyup="activeButton()" auto-grow :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.aboutme')" rows="1" counter :maxlength="maxlengthDescription" v-model="description">
					</v-textarea>
				</v-flex>
			</v-layout>
			<v-layout row wrap align-start justify-end fill-height>
				<v-flex text-xs-left text-md-left xs12 md8 pl-1>
					<span class="requeridTitles">* {{$t('message.requiredFields')}}</span>
				</v-flex>
				<v-flex sm12 xs12 md2 class="profile-flex-padding ">
					<v-btn large block :color="currentBussinesClub.theme.secondary" class="rounded-buttons" dark @click="clearFields"> {{$t('message.restartAll')}}
					</v-btn>
				</v-flex>
				<v-flex sm12 xs12 md2 class="profile-flex-padding ">
					<v-btn :disabled="disabledButon" :dark="activeDark" large block :color="currentBussinesClub.theme.primary" :loading="loadingSave" @click="saveDataPersonalInformation()" class="rounded-buttons"> {{$t('message.saveChanges')}}
					</v-btn>
				</v-flex>
			</v-layout>
		</v-card>
	</v-form>
</div>
</template>
<script>
import Vue from 'vue';
import {
	mapGetters
} from "vuex";
import axios from "axios";
import AppConfig from "../../../constants/AppConfig.js";
import {
	EventBus
} from "../../../eventBus.js";
import {
	setTimeout
} from 'timers';
import TelephoneInput from 'Components/TelephoneInput/vue-tel-input'
import CityAddressPicker from 'Components/CityAddressPicker/cityAddressPicker'
export default {
	props: {
		'currentTab': Number,
	},
	components: {
		'v-telephone-input': TelephoneInput,
		'v-city-address-picker': CityAddressPicker,
	},
	watch: {
		currentTab(value) {
			if (value === 0) {
				this.reloadFields();
				this.resetChanges();
			}
		},
		names(value) {
			if (!value) {
				this.activeButton();
			}
		},
		surnames(value) {
			if (!value) {
				this.activeButton();
			}
		},
		identity(value) {
			if (!value) {
				this.activeButton();
			}
		},
		birthdate(value) {
			if (!value) {
				this.activeButton();
			}
		},
		phone(value) {
			if (!value) {
				this.activeButton();
			}
		},
		birthdateMenu(val) {
			val && setTimeout(() => (this.$refs.birthdatePicker.activePicker = 'YEAR'))
		}
	},
	created() {
		this.birthdate = this.userProfile.personal_info.birthdate
		this.required = this.$t('message.required');
		this.maxLengthText = this.$t('message.maxLengthText');
		this.identificationRules = [v => !!v || this.required]
		this.birthdateRules = [v => !!v || this.required]
		this.namesRules = [
			v => !!v || this.required,
		]
		this.phoneRules = [
			v => !!v || this.required,
			v =>
			/^[0-9+]{2,40}$/.test(
				v
			) || this.maxLengthText
		]

		Vue.nextTick(() => {
			/* Load country information */
			this.countryLoading = true;
			this.$store.dispatch("doGetCountries")
				.finally(() => {
					this.countryLoading = false;
				});

			/* Load gender information */
			this.genderLoading = true;
			this.$store.dispatch("doGetGenders").finally(() => {
				this.genderLoading = false;
			});

			/* Load marital status */
			this.maritalStatusLoading = true;
			this.$store.dispatch("doGetMaritalStatus").finally(() => {
				this.maritalStatusLoading = false;
			});

			/* Load ocupation information */
			this.ocupationLoading = true;
			this.$store.dispatch("doGetOccupationalSector").finally(() => {
				this.ocupationLoading = false;
			});


			EventBus.$on('personal_info:saveChanges', data => {
				this.saveDataPersonalInformation();
			})
			EventBus.$on('personal_info:cancelChanges', data => {
				//this.resetChanges();
			});
		});
	},
	mounted() {
		this.reloadFields();
	},
	data() {
		return {
			birthdateRules: [],
			names: null,
			surnames: null,
			identity: null,
			email: null,
			country: null,
			province: null,
			city: null,
			birthdate: null,
			prefix: null,
			phone: null,
			phoneValid: true,
			description: null,
			countryBirth: null,
			occupation: null,
			gender: null,
			maritalState: null,
			//
			activeHover: true,
			maxLengthTex: '',
			required: '',
			failText: '',
			infoUpdateText: '',
			loadingSave: false,
			valid: true,
			//var names rules
			identificationRules: [],
			namesRules: [],

			maxlengthDescription: 200,
			//var for send data
			personalInformation: {
				userId: "",
				type: "",
				data: {}
			},
			// Rules
			//var for validate form
			valid: true,
			birthdateMenu: false,
			modal: false,
			civilStateList: [],
			prefixCountries: [],
			cityItems: [],
			readOnlyProvince: true,
			readOnlyCity: true,
			auxPrefix: [],
			activeDark: false,
			disabledButon: true,
			countryLoading: false,
			provinceLoading: false,
			cityLoading: false,
			maritalStatusLoading: false,
			genderLoading: false,
			ocupationLoading: false,
			phoneRules: [],
		};
	},
	computed: {
		...mapGetters([
			"userProfile",
			"genders",
			"maritalStatus",
			"countries",
			"provinces",
			"prefixes",
			"currentBussinesClub",
			"jobGroups",
			"selectedLocale"
		]),
		userProfile: {
			get() {
				if (this.$store.getters.userProfile != '')
					return JSON.parse(this.$store.getters.userProfile);
			}
		}
	},

	methods: {
		save(date) {
			this.$refs.birthdateMenu.save(date);
			this.birthdateMenu = false;
		},
		reloadFields() {
			this.names = Vue.getObjectPath(this.userProfile, 'personal_info.names', '');
			this.surnames = Vue.getObjectPath(this.userProfile, 'personal_info.surnames', '');
			this.identity = Vue.getObjectPath(this.userProfile, 'personal_info.identity', '');
			this.email = Vue.getObjectPath(this.userProfile, 'personal_info.email', '');
			this.country = Vue.getObjectPath(this.userProfile, 'personal_info.addresses.address.country', '');
			this.province = Vue.getObjectPath(this.userProfile, 'personal_info.addresses.address.province', '');
			this.city = Vue.getObjectPath(this.userProfile, 'personal_info.addresses.address.city', '');
			this.birthdate = Vue.getObjectPath(this.userProfile, 'personal_info.birthdate', '');
			this.prefix = Vue.getObjectPath(this.userProfile, 'personal_info.addresses.phone.prefix', '');
			this.phone = Vue.getObjectPath(this.userProfile, 'personal_info.addresses.phone.number', '');
			this.description = Vue.getObjectPath(this.userProfile, 'personal_info.description', '');
			this.countryBirth = Vue.getObjectPath(this.userProfile, 'personal_info.birth_country', '');
			this.occupation = parseInt(Vue.getObjectPath(this.userProfile, 'personal_info.occupation', -1));
			this.gender = Vue.getObjectPath(this.userProfile, 'personal_info.gender_flag_code', '');
			this.maritalState = Vue.getObjectPath(this.userProfile, 'personal_info.marital_status_flag_code', '');
		},
		clearFields() {
			/* Clear the fields values */
			this.names = null;
			this.surnames = null;
			this.identity = null;
			this.country = null;
			this.province = null;
			this.city = null;
			this.birthdate = '';
			this.phone = '';
			this.description = '';
			this.countryBirth = null;
			this.occupation = null;
			this.gender = null;
			this.maritalState = null;

			/* Reset the form components */
			this.$refs.names.reset();
			this.$refs.surnames.reset();
			this.$refs.identification.reset();
			this.$refs.birthdate.reset();
			this.$refs.number.reset();
			this.$refs.description.reset();
			this.$refs.gender.reset();
			this.$refs.birthCountry.reset();
			this.$refs.ocupation.reset();
			this.$refs.marital.reset();

			/* Reset the full form */
			this.$refs.personalInfo.reset();

			this.activeButton();
		},

		resetChanges() {
			this.activeDark = false
			this.disabledButon = true
			this.$store.dispatch("resetProfileChanges");
		},

		activeButton() {
			this.activeDark = true
			this.disabledButon = false
			this.$store.dispatch("activateProfileChanges", {
				view: 'personal_info'
			})
		},

		reset() {
			this.$refs.personalInfo.reset();
		},

		/**
		 * Event triggered on phone number change
		 */
		onInput(payload) {
			this.phoneValid = payload.isValid;
			this.phone = payload.number;
			if (this.phoneValid) {
				this.activeButton();
			}
		},

		/**
		 * Store the profile personal information
		 */
		saveDataPersonalInformation() {
			if (!this.$refs.personalInfo.validate() || !this.phoneValid) {
				EventBus.$emit('canNotSave', {
					block: true,
					currentView: 'personal_info'
				});

				if (!this.phoneValid) {
					this.$store.dispatch("doNotification", {
						msg: this.$t("message.errorInvalidPhone"),
						type: 'error'
					});
					return;
				}
				this.$store.dispatch("doNotification", {
					msg: this.$t("message.errorInvalidFields"),
					type: 'error'
				});
				return;
			}

			this.loadingSave = true;
			let personalInfo = {
				names: this.names,
				surnames: this.surnames,
				identity: this.identity,
				phone: this.phone,
				country: this.country,
				province: this.province,
				city: this.city,
				gender: this.gender,
				birthdate: this.birthdate,
				description: this.description,
				birthCountry: this.countryBirth,
				occupation: this.occupation,
				maritalStatus: this.maritalState,
			}

			this.$store.dispatch("updateProfileBasicInformation", personalInfo)
				.then(data => {
					this.$store.dispatch("doNotification", {
						msg: this.$t('message.infoUpdate'),
						type: 'success'
					});
					this.resetChanges();
				}).catch(() => {
					this.$store.dispatch("doNotification", {
						msg: this.$t("message.sessionErrorDefault"),
						type: 'error'
					});
				}).finally(() => {
					this.loadingSave = false;
				});
		},

		changeAddress(payload) {
			this.country = payload.country;
			this.province = payload.province;
			this.city = payload.city;
			this.activeButton();
		},

		/**
		 * Calculate the maximal date allowed to register as birthddate
		 */
		calculateMax() {
			let maxDate = new Date();
			maxDate.setFullYear(maxDate.getFullYear() - 13)
			const month = ("0" + (maxDate.getMonth() + 1)).slice(-2);
			const date = ("0" + maxDate.getDate()).slice(-2);
			const year = maxDate.getFullYear();
			const formattedDate = year + "-" + month + "-" + date;
			return formattedDate;
		},
	}
};
</script>
<style >
.requeridTitles {
	font-size: 12px;
	color: gray
}
</style>
