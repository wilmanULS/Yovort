<template>
<v-flex xs12 md6 lazy>
	<v-card class="colorBorderCards" color="transparent" :class="{'mr05': $vuetify.breakpoint.mdAndUp}">
		<v-card-title>
			<span class="titleCard darker-titles">{{$t('message.jobs')}}</span>
			<v-btn small :color="currentBussinesClub.theme.primary" fab dark absolute right @click="openJobDialog()">
				<v-icon>add</v-icon>
			</v-btn>
		</v-card-title>
		<v-layout row align-center justify-end>
		</v-layout>
		<v-card-text>
			<v-container class="no-padding">
				<v-layout justify-center column fill-height v-if="businessInfo.length==0">
					<span class="noContent">{{$t('message.empty')}}</span>
				</v-layout>
				<v-timeline dense clipped v-else>
					<v-slide-x-transition group>
						<v-timeline-item v-for="(item, index) in businessInfo" :key="index" class="mb-3" :color="currentBussinesClub.theme.primary">
							<template v-slot:icon>
								<v-btn flat icon color="white" @click="editJob(item, index)">
									<v-icon small>edit</v-icon>
								</v-btn>
							</template>
							<v-layout justify-space-between>
								<v-flex xs12>
									<v-list-tile-content>
										<v-list-tile-title class="titleJob darker-titles" v-html="item.roles[0]">
										</v-list-tile-title>
										<v-list-tile-sub-title class="darker-titles" v-html="item.company_name"></v-list-tile-sub-title>
										<v-list-tile-sub-title class="darker-titles" v-if="item.current==true && getObjectPath(item, 'year.from','') !=''" v-html="getObjectPath(item, 'year.from','') +' - ' +$t('message.present')">
										</v-list-tile-sub-title>
										<v-list-tile-sub-title class=" darker-titles" v-else-if="getObjectPath(item, 'year.from','')!= null&& getObjectPath(item, 'year.to','')!=null"
										 v-html="getObjectPath(item, 'year.from','') +' - '+getObjectPath(item, 'year.to','')">
										</v-list-tile-sub-title>
										<v-list-tile-sub-title class="darker-titles" v-if="getObjectPath(item, 'address.location.province','')==''  && getObjectPath(item, 'address.location.city','')==''">
										</v-list-tile-sub-title>
										<v-list-tile-sub-title class="darker-titles" v-else v-html="getObjectPath(item, 'address.location.province','')  +' - '+getObjectPath(item, 'address.location.city','')">
										</v-list-tile-sub-title>
									</v-list-tile-content>
								</v-flex>
							</v-layout>
						</v-timeline-item>
					</v-slide-x-transition>
				</v-timeline>
			</v-container>
		</v-card-text>
	</v-card>
	<v-popup-dialog ref="formJob" :model="businessDialogAdd" :title="jobIndex<0?$t('message.addJob'):$t('message.editJob')" showRequired :showRemove="jobIndex>=0" @onClose="closeAdd" @onSave="saveJob" @onRemove="openDeleteDialog">
		<v-layout row wrap>
			<v-flex xs12>
				<v-text-field ref="nameCompanyAdd" :rules="businessNameRules" required :color="currentBussinesClub.theme.primary" :label="$t('message.companyName')+' *'" v-model="newJob.company_name"></v-text-field>
			</v-flex>
			<v-flex xs6 class="profile-flex-padding" v-if="!newJob.current">
				<v-select class="mr05" :color="currentBussinesClub.theme.primary" browser-autocomplete @blur="changeFocusYear(getObjectPath(newJob, 'year.from',''))" @change="changeFocusYear(getObjectPath(newJob, 'year.from',''))"
				 :label="$t('message.from')" :no-data-text="$t('message.nodatafound')" ref="yearFrom" append-icon="event" :menu-props="{contentClass:'menuClass'}" :items="yearsArray" v-model="newJob.year.from"></v-select>
			</v-flex>
			<v-flex xs12 class="profile-flex-padding" v-else>
				<v-select :color="currentBussinesClub.theme.primary" browser-autocomplete @blur="changeFocusYear(getObjectPath(newJob, 'year.from',''))" @change="changeFocusYear(getObjectPath(newJob, 'year.from',''))" :label="$t('message.from')"
				 :no-data-text="$t('message.nodatafound')" ref="yearFrom" append-icon="event" :menu-props="{contentClass:'menuClass'}" :items="yearsArray" v-model="newJob.year.from"></v-select>
			</v-flex>
			<v-flex xs6 class="profile-flex-padding" v-if="!newJob.current">
				<v-select class="ml05" :color="currentBussinesClub.theme.primary" append-icon="event" :items="yearstoArray" id="yearTo" :label="$t('message.to')" :menu-props="{contentClass:'menuClass'}" :no-data-text="$t('message.nodatafound')"
				 v-model="newJob.year.to">
				</v-select>
			</v-flex>
			<v-flex xs12 class="profile-flex-padding">
				<v-container class="no-padding">
					<v-checkbox class="no-space" :color="currentBussinesClub.theme.primary" v-model="newJob.current" :label="$t('message.currentlyMessageJob')" hide-details></v-checkbox>
				</v-container>
			</v-flex>
			<v-flex xs12 class="profile-flex-padding">
				<v-container class="no-padding">
					<v-city-address-picker :country="newJob.addresses.address.location.country" :province="newJob.addresses.address.location.province" :city="newJob.addresses.address.location.city" @onValueChange="changeAddress">
					</v-city-address-picker>
				</v-container>
			</v-flex>
			<v-flex xs12 md6 lg6 class="profile-flex-padding">
				<v-layout column fill-height>
					<v-layout row class="fix-row-height mb-1">
						<v-text-field class="mb-1" :class="{'mr05': $vuetify.breakpoint.mdAndUp}" clearable append-icon="check" @click:append="addNewRole" @keyup.enter="addNewRole" v-model="newRol" name="name" :label="$t('message.addRolePlayed')"
						 hide-details :color="currentBussinesClub.theme.primary"></v-text-field>
					</v-layout>
					<v-layout row wrap fill-height>
						<v-card-text class="borderCards" fill-height :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }">
							<v-container>
								<v-layout align-center justify-center column fill-height v-if="newJob.roles.length<1">
									<span class="noContent">{{$t('message.emptyroles')}}</span>
								</v-layout>
								<div v-for="(rol, index) in newJob.roles" :key="index">
									<v-chip style="background: white!important;" ref="chipRoles" close @input="openDialogRoles(index)">{{rol}}</v-chip>
								</div>
							</v-container>
						</v-card-text>
					</v-layout>
				</v-layout>
			</v-flex>
			<v-flex xs12 md6 lg6 class="profile-flex-padding">
				<v-layout column fill-height>
					<v-layout row class="fix-row-height mb-1">
						<v-telephone-input class="mb-1" :class="{'ml05': $vuetify.breakpoint.mdAndUp}" ref="number" :disabledFetchingCountry="true" v-model="number" :color="currentBussinesClub.theme.primary" :placeholder="$t('message.phoneNumber')"
						 hide-details @onInput="onInput" @onEnter="addNewPhone" appendIcon="check" :preferredCountries="['ec']">
						</v-telephone-input>
					</v-layout>
					<v-layout row wrap fill-height>
						<v-card-text class="borderCards" fill-height :class="{'ml05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }">
							<v-container>
								<v-layout align-center justify-center column fill-height v-if="newJob.addresses.address.phones.length<1 ">
									<span class="noContent">{{$t('message.emptyphones')}}</span>
								</v-layout>
								<div v-for="(phone, index) in newJob.addresses.address.phones" :key="index">
									<v-chip style="background: white!important;" ref="chipPhones" close @input="openDialogPhones(index)">{{phone.number}}</v-chip>
								</div>
							</v-container>
						</v-card-text>
					</v-layout>
				</v-layout>
			</v-flex>
			<v-flex xs12 class="profile-flex-padding">
				<v-layout column fill-height>
					<v-layout row>
						<v-text-field class="mb-1" clearable append-icon="check" @click:append="addNewEmail" @keyup.enter="addNewEmail" v-model="newEmail" name="name" :label="$t('message.addEmail')" :color="currentBussinesClub.theme.primary"
						 ref="emailAdd" hide-details></v-text-field>
					</v-layout>
					<v-layout row wrap fill-height>
						<v-card-text class="borderCards" fill-height>
							<v-container>
								<v-layout align-center justify-center column fill-height v-if="newJob.addresses.emails.length<1">
									<span class="noContent">{{$t('message.emptyemails')}}</span>
								</v-layout>
								<div v-for="(email, index) in newJob.addresses.emails" :key="index">
									<v-chip style="background: white!important;" ref="chipMails" close @input="openDialogEmail(index)">{{email}}</v-chip>
								</div>
							</v-container>
						</v-card-text>
					</v-layout>
				</v-layout>
			</v-flex>
		</v-layout>
	</v-popup-dialog>
	<v-confirm-dialog :titleToolbar="$t('message.confirmMessageDeleteRol')" ref="rolDialog" type="question" :heading="$t('message.deleteRoles')" :message="$t('message.confirmMessageDeleteRol')" @onConfirm="removeRoles()"></v-confirm-dialog>
	<v-confirm-dialog :titleToolbar="$t('message.deletePhones')" ref="phoneRemoveDialog" type="question" :heading="$t('message.deletePhones')" :message="$t('message.confirmMessageDeletePhone')" @onConfirm="removePhones"></v-confirm-dialog>
	<v-confirm-dialog :titleToolbar="$t('message.deleteEmail')" ref="emailDialog" type="question" :heading="$t('message.deleteEmail')" :message="$t('message.confirmMessageDeleteEmail')" @onConfirm="removeEmail()"></v-confirm-dialog>
	<v-confirm-dialog :titleToolbar="$t('message.deleteJob')" ref="jobDialog" type="question" :heading="$t('message.deleteJob')" :message="$t('message.confirmMessageDeleteJob')" @onConfirm="deleteItemJob"></v-confirm-dialog>
	<v-confirm-dialog v-bind:titleToolbar="$t('message.discardChanges')" ref="discardBusiness" type="question" :heading="$t('message.changesWithoutSaving')" v-bind:message="$t('message.discardChangesLabel')" @onConfirm="closeDiscardBusiness">
	</v-confirm-dialog>
</v-flex>
</template>
<script>
import Vue from "vue";
import confirmDialog from "Components/ySocialHome/confirmDialog";
import {
	mapGetters
} from "vuex";
import TelephoneInput from 'Components/TelephoneInput/vue-tel-input'
import CityAddressPicker from 'Components/CityAddressPicker/cityAddressPicker'

export default {
	components: {
		"v-confirm-dialog": confirmDialog,
		'v-telephone-input': TelephoneInput,
		'v-city-address-picker': CityAddressPicker,
	},
	data() {
		return {
			editBusinessStatus: false,
			editDialogModified: false,
			businessNameRules: [],
			getObjectPath: Vue.getObjectPath,
			items: "",
			validJob: true,
			validEditJob: true,
			indexChipsRoles: "",
			indexChipsPhones: "",
			indexChipsEmails: "",
			deletingJob: false,
			valid: true,
			//vars for send data

			newJob: {
				company_name: "",
				year: {
					from: "",
					to: ""
				},
				current: false,
				roles: [],
				addresses: {
					address: {
						location: {
							country: "",
							province: "",
							city: ""
						},
						phones: []
					},
					emails: []
				}
			},
			newRol: "",
			newEmail: "",
			prefix: "",
			number: "",
			yearFrom: 1951,
			editNewRol: "",
			yearsArray: [],
			yearstoArray: [],
			businessDialogAdd: false,

			businessDialogEdit: false,
			// array for v-model
			jobArray: [],

			jobIndex: "",
			businessInfo: [],
			fromEdit: "",
			// var for data table jobs
			editBusiness: -1,
			recoveryDataBusiness: [],
			skillsAdd: false,
			newNameSkill: "",
			newPorcentSkill: "",
			timeLineBusiness: [],
			jobId: null,
			settings: {
				maxScrollbarLength: 160
			}
		};
	},
	computed: {
		...mapGetters(["userProfile", "prefixes", 'currentBussinesClub']),
		userProfile: {
			get() {
				if (this.$store.getters.userProfile != '')
					return JSON.parse(this.$store.getters.userProfile);
			}
		},
	},
	watch: {
		'newJob'(val) {
			this.editDialogModified = true;
		},
		userProfile(val) {
			Vue.nextTick(() => {
				this.businessInfo = this.sortedJobs(this.userProfile.business_info);
			});
		},
	},
	mounted() {
		this.$nextTick(() => {
			this.businessInfo = this.sortedJobs(this.userProfile.business_info);
		});
	},

	created() {
		this.yearPicker();
		this.businessNameRules = [(v) => !!v || this.$t('message.companyNameRule')];
	},
	methods: {
		changeAddress(payload) {
			this.newJob.addresses.address.location.country = payload.country;
			this.newJob.addresses.address.location.province = payload.province;
			this.newJob.addresses.address.location.city = payload.city;
			this.editDialogModified = true;
		},
		/**
		 * Event triggered on phone number change
		 */
		onInput(payload) {
			this.phoneValid = payload.isValid;
			this.number = payload.number;
		},

		sortedJobs(jobs) {
			if (!jobs) {
				jobs = [];
			}
			return jobs.sort((a, b) => {
				const fromA = Number(Vue.getObjectPath(a, 'year.from', 0)) || 0;
				let toA = Number(Vue.getObjectPath(a, 'year.to', 0)) || 0;
				const currentA = Boolean(Vue.getObjectPath(a, 'current', false));
				const fromB = Number(Vue.getObjectPath(b, 'year.from', 0)) || 0;
				let toB = Number(Vue.getObjectPath(b, 'year.to', 0)) || 0;
				const currentB = Boolean(Vue.getObjectPath(b, 'current', false));

				if (currentA) {
					if (!currentB) {
						return -1;
					}

					/* Take current year as last year for sorting */
					toA = (new Date()).getFullYear();
					toB = toA;
					return this.compareYears(fromA, toA, fromB, toB);
				} else if (currentB) {
					if (!currentA) {
						return 1;
					}

					/* Take current year as last year for sorting */
					toA = (new Date()).getFullYear();
					toB = toA;
					return this.compareYears(fromA, toA, fromB, toB);
				} else {
					return this.compareYears(fromA, toA, fromB, toB);
				}
			});
		},

		compareYears(fromA, toA, fromB, toB) {
			if (fromA > fromB)
				return -1;
			if (fromA < fromB)
				return 1;
			if (toA > toB)
				return -1;
			if (toA < toB)
				return 1;
			return 0;
		},

		editJob(item, index) {
			this.deletingJob = false;
			this.jobIndex = index;
			this.jobId = Vue.getObjectPath(item, '_id', null);
			this.newJob = {
				_id: this.jobId,
				company_name: Vue.getObjectPath(item, 'company_name', ''),
				year: {
					from: Vue.getObjectPath(item, 'year.from', ''),
					to: Vue.getObjectPath(item, 'year.to', '')
				},
				current: Vue.getObjectPath(item, 'current', false),
				roles: Vue.getObjectPath(item, 'roles', []),
				addresses: {
					address: {
						location: {
							country: Vue.getObjectPath(item, 'addresses.address.location.country', ''),
							province: Vue.getObjectPath(item, 'addresses.address.location.province', ''),
							city: Vue.getObjectPath(item, 'addresses.address.location.city', '')
						},
						phones: Vue.getObjectPath(item, 'addresses.address.phones', []),
					},
					emails: Vue.getObjectPath(item, 'addresses.emails', [])
				}
			};

			this.yearstoArray = [];
			this.changeFocusYear(this.newJob.year.from);

			this.newRol = ""

			this.$refs.nameCompanyAdd.resetValidation();
			this.businessDialogAdd = true
		},
		closeDiscardBusiness() {
			this.$refs.discardBusiness.close()
			this.businessDialogAdd = false;
		},
		discardBusiness() {
			if (this.newJob.company_name != "" || this.newJob.current != "") {
				return true;
			} else {
				return false;
			}

		},
		saveSkill() {
			var porcentSkill = parseInt(this.newPorcentSkill)
			if (porcentSkill > 100) {
				this.$store.dispatch("doNotification", {
					msg: this.$t("message.errorMessageSaveSkill"),
					type: 'error'
				});
			} else {

			}

		},
		openSkillDialog() {
			this.skillsAdd = true
		},
		closeSkillDialog() {
			this.skillsAdd = false
		},

		/**
		 * Show the job dialog
		 */
		openJobDialog() {
			this.jobIndex = -1;
			this.deletingJob = false;
			this.newJob = {
				company_name: "",
				year: {
					from: "",
					to: ""
				},
				current: false,
				roles: [],
				addresses: {
					address: {
						location: {
							country: "",
							province: "",
							city: ""
						},
						phones: []
					},
					emails: []
				}
			};

			this.$refs.formJob.doReset()
			this.$refs.nameCompanyAdd.resetValidation();

			this.businessDialogAdd = true
		},

		closeAdd() {
			this.yearstoArray = [];
			if (this.discardBusiness()) {
				this.$refs.discardBusiness.openDialog()
				this.$refs.nameCompanyAdd.resetValidation();
			} else {
				this.businessDialogAdd = false;
			}
		},

		/**
		 * Add new user role
		 */
		addNewRole() {
			/* Check if the role exists */
			if (!this.newRol || this.newRol.length === 0) {
				return 0;
			}

			/* Check if the role was added previously */
			const roles = this.newJob.roles.filter(value => value.toLowerCase() === this.newRol.toLowerCase());
			if (roles && roles.length > 0) {
				this.$store.dispatch("doNotification", {
					msg: this.$t("message.errorMessagePhoneInUse"),
					type: 'error'
				});
				return -1;
			}

			/* Add the phone to the list */
			this.newJob.roles.push(this.newRol);

			/* Clear values */
			this.newRol = "";
			return 0;
		},

		/**
		 * Remove a role
		 */
		removeRoles() {
			if (this.$refs.rolDialog.open && this.indexChipsRoles >= 0) {
				this.newJob.roles.splice(this.indexChipsRoles, 1);
				this.$refs.rolDialog.close();
				this.indexChipsRoles = -1;
			}
		},


		/**
		 * Add new phone to the current job
		 */
		addNewPhone() {
			/* Check if the phone number exists */
			if (!this.number || this.number.length === 0) {
				return 0;
			}

			/* Check if the phone number is valid */
			if (!this.phoneValid) {
				this.$store.dispatch("doNotification", {
					msg: this.$t("message.errorMessageWrongPhone"),
					type: 'error'
				});
				return -1;
			}

			/* Check if the phone was added previously */
			const phones = this.newJob.addresses.address.phones.filter(value => value.number === this.number);
			if (phones && phones.length > 0) {
				this.$store.dispatch("doNotification", {
					msg: this.$t("message.errorMessagePhoneInUse"),
					type: 'error'
				});
				return -1;
			}

			/* Add the phone to the list */
			this.newJob.addresses.address.phones.push({
				number: this.number
			});

			/* Clear values */
			this.number = "";
			this.phoneValid = false;
			return 0;
		},

		/**
		 * Remove a phone number
		 */
		removePhones() {
			if (this.$refs.phoneRemoveDialog.open && this.indexChipsPhones >= 0) {
				this.newJob.addresses.address.phones.splice(this.indexChipsPhones, 1);
				this.$refs.phoneRemoveDialog.close();
				this.indexChipsPhones = -1;
			}
		},

		/**
		 * Add new email to the current job
		 */
		addNewEmail() {
			/* Check if the email exists */
			if (!this.newEmail || this.newEmail.length === 0) {
				return 0;
			}

			/* Check if the email was added previously */
			const emails = this.newJob.addresses.emails.filter(value => value.toLowerCase() === this.newEmail.toLowerCase());
			if (emails && emails.length > 0) {
				this.$store.dispatch("doNotification", {
					msg: this.$t("message.errorMessageEmailAlreadyExists"),
					type: 'error'
				});
				return -1;
			}

			/* Check if the email is valid */
			if (!/^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(this.newEmail)) {
				this.$store.dispatch("doNotification", {
					msg: this.$t("message.errorMessageWrongEmail"),
					type: 'error'
				});
				return -1;
			}

			/* Add the email to the list */
			this.newJob.addresses.emails.push(this.newEmail);

			/* Clear values */
			this.newEmail = "";

			return 0;
		},

		/**
		 * Remove an email address
		 */
		removeEmail() {
			if (this.$refs.emailDialog.open && this.indexChipsEmails >= 0) {
				this.newJob.addresses.emails.splice(this.indexChipsEmails, 1);
				this.$refs.emailDialog.close();
				this.indexChipsEmails = -1;
			}
		},

		saveJob() {
			/* Try to save values leave into fields */
			let cancelSave = false;
			cancelSave |= this.addNewRole() < 0;
			cancelSave |= this.addNewPhone() < 0;
			cancelSave |= this.addNewEmail() < 0;

			if (cancelSave) {
				return;
			}

			if (this.$refs.formJob.doValidate()) {
				if (this.newJob.current == '' || this.newJob.current == null) {
					this.newJob.current = false
				}

				if (this.jobId) {
					this.updateJob(() => {
						this.businessDialogAdd = false;
					});
				} else {
					this.addJob(() => {
						this.businessDialogAdd = false;
					});
				}
			}
		},

		deleteItemJob() {
			this.deletingJob = true;

			/* Remove the selected job */
			this.$refs.jobDialog.close();
			this.removeJob(() => {
				this.jobIndex = -1;
				this.businessDialogAdd = false;
				this.deletingJob = false;
			});
		},

		/**
		 * Add a new job
		 */
		addJob(cb) {
			const job = {
				companyName: this.newJob.company_name,
				from: this.newJob.year.from,
				to: this.newJob.year.to,
				current: this.newJob.current,
				roles: this.newJob.roles,
				country: this.newJob.addresses.address.location.country,
				province: this.newJob.addresses.address.location.province,
				city: this.newJob.addresses.address.location.city,
				phones: this.newJob.addresses.address.phones.map(value => value.number),
				emails: this.newJob.addresses.emails,
			};
			this.dispatchAction("doProfileAddJob", job, cb);
		},

		/**
		 * Update an existant job
		 */
		updateJob(cb) {
			const job = {
				jobId: this.jobId,
				companyName: this.newJob.company_name,
				from: this.newJob.year.from,
				to: this.newJob.year.to,
				current: this.newJob.current,
				roles: this.newJob.roles,
				country: this.newJob.addresses.address.location.country,
				province: this.newJob.addresses.address.location.province,
				city: this.newJob.addresses.address.location.city,
				phones: this.newJob.addresses.address.phones.map(value => value.number),
				emails: this.newJob.addresses.emails,
			};
			this.dispatchAction("doProfileUpdateJob", job, cb);
		},

		/**
		 * Remove a job
		 */
		removeJob(cb) {
			const job = {
				jobs: [this.jobId],
			};
			this.dispatchAction("doProfileRemoveJob", job, cb);
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

		openDialogRoles(index) {
			this.indexChipsRoles = index;
			this.$refs.rolDialog.openDialog();
		},

		openDialogPhones(index) {
			this.indexChipsPhones = index;
			this.$refs.phoneRemoveDialog.openDialog();
		},

		openDialogEmail(index) {
			this.indexChipsEmails = index;
			this.$refs.emailDialog.openDialog();
		},

		yearPicker() {
			let end = new Date().getFullYear();
			let options = "";
			for (let year = this.yearFrom; year <= end; year++) {
				this.yearsArray.push(year);
			}
			this.yearsArray.reverse();
		},

		changeFocusYear(from) {
			let end = new Date().getFullYear();
			this.yearstoArray = [];
			if (from != "") {
				for (let index = from; index <= end; index++) {
					this.yearstoArray.push(index);
				}
				this.yearstoArray.reverse();
			}
		},

		changeSelected() {
			let end = new Date().getFullYear();
			for (let index = this.fromEdit; index <= end; index++) {
				this.yearstoArray.push(index);

			}
			this.yearstoArray.reverse();
		},

		openDeleteDialog() {
			this.$refs.jobDialog.openDialog();
		},
	}
};
</script>
<style scoped>
.no-space {
	padding: 0 !important;
	margin: 0 !important;
}

.fix-row-height {
	height: 64px !important;
	min-height: 64px !important;
	max-height: 64px !important;
	margin: 0 !important;
	padding: 0 !important;
}
</style>
