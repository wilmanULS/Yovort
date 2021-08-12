<template>
<div v-if="currentTab === 1 && tabFunction===0 " class="animated fadeIn">
	<v-form id="v-step-welcome" onSubmit="return false;" class="light_field mb-1" ref="register" lazy-validation>
		<v-layout row wrap>
			<v-flex xs12>
				<v-text-field id="v-step-names" :label="$t('message.nameRegister')+' *'" browser-autocomplete="nonex" :rules="registerNamesRules" v-model="registerNames" solo background-color="light" flat></v-text-field>
			</v-flex>
			<v-flex xs12>
				<v-text-field id="v-step-surnames" :label="$t('message.lastNameRegister')+' *'" broser-autocomplete="nonex" :rules="registerLastnamesRules" v-model="registerLastnames" solo background-color="light" flat>
				</v-text-field>
			</v-flex>
			<v-flex xs12>
				<v-menu class="pt-0 pb-0" ref="registerBirthdateMenu" v-model="registerBirthdateMenu" :close-on-content-click="false" :nudge-right="40" :return-value.sync="registerBirthdate" lazy transition="scale-transition" offset-y full-width
				 min-width="290px">
					<template v-slot:activator="{ on }">
						<v-text-field id="v-step-birthdate" v-model="registerBirthdate" ref="registerBirthdate" readonly v-on="on" :color="currentBussinesClub.theme.primary" solo background-color="light" v-bind:label="$t('message.birthdate')+' *'"
						 append-icon="event" :rules="registerBirthdateRules"></v-text-field>
					</template>
					<v-date-picker v-model="registerBirthdate" ref="registerBirthdatePicker" :max="calculateMax()" :color="currentBussinesClub.theme.primary" :locale="selectedLocale.locale" @change="save">
					</v-date-picker>
				</v-menu>
			</v-flex>
			<v-flex xs12>
				<v-text-field id="v-step-email" :rules="registerEmailRules" type="email" browser-autocomplete="nonex" v-model="registerEmail" :label="$t('message.emailRegister')+' *'" solo background-color="light" @change="validateEmailAddress"
				 :loading="registerEmailChecking" :hint="registerEmailHint" persistent-hint></v-text-field>
			</v-flex>
			<v-flex xs12>
				<v-text-field id="v-step-password" :rules="registerPasswordRules" browser-autocomplete="new-password" @click:append="registerShowPassword = !registerShowPassword" :type="registerShowPassword ? 'text' : 'password'"
				 :append-icon="registerShowPassword ? 'mdi mdi-eye' : 'mdi mdi-eye-off'" v-model="registerPassword" :label="$t('message.passwordRegister') + ' *'" solo background-color="light" :color="currentBussinesClub.theme.primary" required>
				</v-text-field>
			</v-flex>
			<v-flex xs12>
				<v-text-field id="v-step-vcode" :rules="registerVCodeRules" type="text" browser-autocomplete="nonex" :readonly="registerVCodeReadonly" v-model="registerVCode" :label="$t('message.invitedByRegister')" solo background-color="light"
				 @change="validateVCode" :hint="registerVCodeHint" persistent-hint :loading="registerVCodeChecking" flat></v-text-field>
			</v-flex>
			<v-flex xs12 style>
				<v-checkbox id="v-step-terms" :color="currentBussinesClub.theme.primary" v-model="registerAcceptTerms" dark flat :ripple="false" content-class="activeCheckbox">
					<template v-slot:label>
						<div>
							{{$t('message.accept')}}
							<template>
								<a @click="viewTermsConditions()" @click.stop class="white--text" style="text-decoration:underline !important">{{$t('message.aceptTermAndConditions')}}</a>
							</template>
						</div>
					</template>
				</v-checkbox>
			</v-flex>
		</v-layout>
		<v-layout row wrap>
			<v-flex xs4>
				<v-btn v-if="registerAcceptTerms" :loading="registerLoading" depressed class="rounded-login-buttons rounded-login-buttons-text" @click="registerUser">{{$t('message.register')}}</v-btn>
			</v-flex>
		</v-layout>
		<v-tour name="registerTour" :steps="steps" :options="tourOptions"></v-tour>
	</v-form>
	<v-layout row wrap align-center justify-center>
		<v-flex xs12>
			<div class="text-xs-center">
				<span class="pointer-hand" @click="startHelpTour">{{$t(' message.needHelp')}}</span>
			</div>
		</v-flex>
	</v-layout>
	<v-terms-dialog ref="termsDialog" :heading="$t(' message.termsConditions')"></v-terms-dialog>
	<v-rules-club-dialog ref="rulesClub"></v-rules-club-dialog>
</div>
</template>
<script>
import Vue from "vue";
import {
	mapGetters
} from "vuex";
import router from "../../router";
import ERRORS from "../../constants/errors";
import TermsDialog from "./loginTermsDialog";
import RulesClub from "./rulesClubDialog";

export default {
	props: {
		currentTab: Number,
		tabFunction: Number,
		vcode: String
	},
	computed: {
		...mapGetters(["currentBussinesClub", "selectedLocale"])
	},
	watch: {
		currentTab() {
			if (this.currentTab === 1 && this.tabFunction === 0) this.clearForm();
		},
		vcode(value) {
			this.registerVCode = value;
			this.registerVCodeReadonly = true;
			this.validateVCode();
		},
		registerBirthdateMenu(val) {
			val && setTimeout(() => (this.$refs.registerBirthdatePicker.activePicker = 'YEAR'))
		}
	},
	components: {
		"v-terms-dialog": TermsDialog,
		"v-rules-club-dialog": RulesClub
	},
	data() {
		return {
			registerLoading: false,
			/* User account name */
			registerNames: "",
			registerNamesRules: [],
			/* User account lastname */
			registerLastnames: "",
			registerLastnamesRules: [],
			/* User account birthdate */
			registerBirthdate: null,
			registerBirthdateRules: [],
			registerBirthdateMenu: false,
			/* User account email address */
			registerEmail: null,
			registerEmailRules: [],
			registerEmailChecking: false,
			registerEmailValid: false,
			registerEmailHint: "",
			/* User account password */
			registerPassword: "",
			registerPasswordRules: [],
			registerShowPassword: false,
			/* User account referreal vCode */
			registerVCode: null,
			registerVCodeRules: [],
			registerVCodeReadonly: false,
			registerVCodeChecking: false,
			registerVCodeValid: true,
			registerVCodeHint: "",
			/* Terms and conditions */
			registerAcceptTerms: false,

			date: null,
			menu: false,

			/* Tour component options */
			tourOptions: {
				useKeyboardNavigation: false,
				labels: {
					buttonSkip: this.$t('message.tourSkip'),
					buttonPrevious: this.$t('message.tourPrevious'),
					buttonNext: this.$t('message.tourNext'),
					buttonStop: this.$t('message.tourFinish')
				}
			},

			/* Tour steps */
			steps: [{
					target: '#v-step-names',
					content: this.$t('message.tourRegisterNames')
				},
				{
					target: '#v-step-surnames',
					content: this.$t('message.tourRegisterSurnames')
				},
				{
					target: '#v-step-birthdate',
					content: this.$t('message.tourRegisterBirthdate')
				},
				{
					target: '#v-step-email',
					content: this.$t('message.tourRegisterEmail')
				},
				{
					target: '#v-step-password',
					content: this.$t('message.tourRegisterPassword')
				},
				{
					target: '#v-step-vcode',
					content: this.$t('message.tourRegistervCode')
				},
				{
					target: '#v-step-terms',
					content: this.$t('message.tourRegisterTerms')
				},
			],
		};
	},
	created() {
		this.registerNamesRules = [
			v => !!v || "* " + this.$t("message.sessionNameRequired")
		];
		this.registerLastnamesRules = [
			v => !!v || "* " + this.$t("message.sessionLastNameRequired")
		];
		this.registerBirthdateRules = [
			v => !!v || "* " + this.$t("message.sessionBirthdateRequired")
		];
		this.registerEmailRules = [
			v => !!v || "* " + this.$t("message.emailRequiredRuleMessage")
		];
		this.registerPasswordRules = [
			v => !!v || "* " + this.$t("message.passwordRequired"),
			v =>
			(v && /(?=.*?[A-Z])(?=.*?[a-z])(?=.*?[0-9]).{8,}$/.test(v)) ||
			"* " + this.$t("message.strengthPasswordWeak")
		];
		this.registerVCodeRules = [];
	},
	mounted() {},
	methods: {
		/**
		 * Start the form help tour
		 */
		startHelpTour() {
			this.$tours['registerTour'].start()
		},
		save(date) {
			this.$refs.registerBirthdateMenu.save(date);
			this.registerBirthdateMenu = false;
		},
		/**
		 * Clear the signin form
		 */
		clearForm() {
			this.registerLoading = false;
			this.registerNames = "";
			this.registerLastnames = "";
			this.registerBirthdate = "";
			this.registerBirthdateMenu = false;
			this.registerEmail = null;
			this.registerEmailChecking = false;
			this.registerEmailValid = false;
			this.registerEmailHint = "";
			this.registerPassword = "";
			this.registerShowPassword = false;
			this.registerVCodeChecking = false;
			this.registerVCodeValid = true;
			this.registerVCodeHint = "";
			this.registerAcceptTerms = false;
			this.registerVCode = this.vcode;
			this.registerVCodeReadonly = true;
			if (this.$refs.register) {
				this.$refs.register.resetValidation();
				this.$refs.register.reset();
			}
			this.validateVCode();
		},

		/**
		 * Calculate the maximal date allowed to register as birthddate
		 */
		calculateMax() {
			let maxDate = new Date();
			maxDate.setFullYear(maxDate.getFullYear() - 13);
			const month = ("0" + (maxDate.getMonth() + 1)).slice(-2);
			const date = ("0" + maxDate.getDate()).slice(-2);
			const year = maxDate.getFullYear();
			const formattedDate = year + "-" + month + "-" + date;
			return formattedDate;
		},

		/**
		 * Validate if an email inserted is valid or there is an user registered with it
		 */
		validateEmailAddress() {
			/* Check for mail contains values */
			if (
				!this.registerEmail ||
				!/^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(this.registerEmail)
			) {
				this.registerEmailValid = false;
				this.registerEmailHint = "* " + this.$t("message.sessionEmailInvalid");
				return;
			}

			/*Validate if email exists on IAM*/
			this.registerEmailChecking = true;
			this.$store
				.dispatch("doCheckEmail", {
					email: this.registerEmail
				})
				.then(existEmail => {
					/*If email is already registered on IAM*/
					if (existEmail.data.data.registered.iam == true) {
						this.registerEmailValid = false;
						this.registerEmailHint =
							"* " + this.$t("message.sessionEmailInUse");
					} else if (!existEmail.data.data.valid) {
						/* Email is invalid */
						this.registerEmailValid = false;
						this.registerEmailHint = this.$t("message.sessionEmailInvalid");
					} else {
						/* Email address is valid and can be used to register new user */
						this.registerEmailValid = true;
						this.registerEmailHint = "";
					}
				})
				.catch(err => {
					this.$store.dispatch("doNotification", {
						msg: this.$t("message.sessionErrorDefault"),
						type: "error"
					});
				})
				.finally(() => {
					this.registerEmailChecking = false;
				});
		},

		/**
		 * This method validates if vCode entered has an valid user associated with it.
		 **/
		validateVCode() {
			/* If the vCode don't contains value its valid */
			if (!this.registerVCode) {
				this.registerVCodeHint = "";
				this.registerVCodeValid = true;
				this.registerVCodeReadonly = false;
				return;
			}

			/* Check for vCode length */
			if (this.registerVCode.length !== 8) {
				this.registerVCodeValid = false;
				this.registerVCodeReadonly = false;
				this.registerVCodeReadonly = false;
				this.registerVCodeHint = "* " + this.$t("message.vCodeInvalid");
				return;
			}

			/* Validate the vCode valid against the server */
			this.registerVCodeChecking = true;
			this.$store
				.dispatch("doValidateVCode", {
					vCode: this.registerVCode
				})
				.then(vCode => {
					if (vCode.data.data.valid) {
						this.registerVCodeHint = "";
						this.registerVCodeValid = true;
					} else {
						this.registerVCodeValid = false;
						this.registerVCodeReadonly = false;
						this.registerVCodeHint = "* " + this.$t("message.vCodeInvalid");
					}
				})
				.catch(err => {
					this.$store.dispatch("doNotification", {
						msg: this.$t("message.sessionErrorDefault"),
						type: "error"
					});
				})
				.finally(() => {
					this.registerVCodeChecking = false;
				});
		},

		/**
		 * Register new user account into the system
		 */
		registerUser() {
			/* Validate the form */
			if (
				!this.$refs.register.validate() ||
				!this.registerAcceptTerms ||
				!this.registerEmailValid ||
				!this.registerVCodeValid
			) {
				return;
			}

			this.registerLoading = true;
			/* Prepare the data object to register new user account */
			let newUserAccount = {
				email: this.registerEmail,
				password: this.registerPassword,
				/* Information to send directly to the business club */
				appPayload: {
					business_club_id: this.currentBussinesClub.clubId,
					personal_info: {
						names: this.registerNames,
						surnames: this.registerLastnames,
						picture: "/static/img/perfil_yovirt.png",
						banner: "/static/img/ciudad_banner_yovirt.gif",
						birthdate: this.registerBirthdate,
						email: this.registerEmail,
						refered_by: this.registerVCode
					},
					setting: {
						friend_request_flag_code: 0,
						profile_visibility_flag_code: 0,
						notification: {
							activity_sound: true,
							activity_email: true,
							birthday: true,
							inactive_chat_message: true
						}
					}
				}
			};

			/* Register user account on IAM */
			this.$store
				.dispatch("doRegisterUser", newUserAccount)
				.then(register => {
					this.$store.dispatch("doNotification", {
						msg: this.$t("message.sendTokenForValidation"),
						type: "success"
					});
					this.confirmAccount(register.data.data.userId);
					this.clearForm();
				})
				.catch(err => {
					const error = Vue.getObjectPath(err, "response.data.error", -1);
					switch (error) {
						case ERRORS.IAM.INVALID_PASSWORD_REQUIREMENTS:
							this.$store.dispatch("doNotification", {
								msg: this.$t("message.sessionPasswordWeak"),
								type: "error"
							});
							break;
						default:
							this.$store.dispatch("doNotification", {
								msg: this.$t("message.sessionErrorDefault"),
								type: "error"
							});
							break;
					}
				})
				.finally(() => {
					this.registerPassword = "";
					this.registerLoading = false;
				});
		},

		/**
		 * Show terms and conditions dialog
		 */
		viewTermsConditions() {
			this.$refs.termsDialog.openDialog();
		},
		viewRulesClub() {
			this.$refs.rulesClub.openDialog();
		},

		/**
		 * Show the confirmation view because user need to confirm the account
		 */
		confirmAccount(userId) {
			this.$emit("confirmAccount", {
				email: this.registerEmail,
				userId: userId,
				first: true
			});
		}
	}
};
</script>
<style scoped>
.activeCheckbox {
	position: absolute !important;
	top: 0px;
	color: Red;
}
</style>
