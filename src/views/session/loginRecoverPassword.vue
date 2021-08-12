<template>
<div v-if="currentTab === 1 && tabFunction===2 " class="animated fadeIn">
	<v-form onSubmit="return false;" class="light_field mb-1" ref="recover" lazy-validation>
		<v-text-field id="v-step-email" ref="recoverEmail" :rules="recoverEmailRules" type="email" browser-autocomplete="nonex" v-model="recoverEmail" :readonly="recoverStep>0" :label="$t('message.emailRecoverPassword')+' *'" solo
		 background-color="light" required @keyup.enter="sendRecoverMail" flat :hint="recoverEmailHint" persistent-hint>
		</v-text-field>
		<v-text-field id="v-step-token" :rules="recoverTokenRules" type="text" browser-autocomplete="nonex" v-model="recoverToken" v-if="recoverStep>0" :readonly="recoverStep>1" :label="$t('message.validateTokenRecover')+' *'" solo
		 background-color="light" required @keyup.enter="validateRecoverToken" flat :hint="recoverTokenHint" persistent-hint>
		</v-text-field>
		<v-text-field id="v-step-password" :rules="recoverPasswordRules" browser-autocomplete="new-password" v-model="recoverPassword" v-if="recoverStep>1" :label="$t('message.setNewPassword')+' *'"
		 @click:append="recoverPasswordShow = !recoverPasswordShow" :type="recoverPasswordShow ? 'text' : 'password'" :append-icon="recoverPasswordShow ? 'mdi mdi-eye' : 'mdi mdi-eye-off'" solo background-color="light" required
		 @keyup.enter="resetAccountPassword">
		</v-text-field>
		<v-layout row wrap>
			<v-flex xs4>
				<v-btn id="v-step-button-send" :loading="recoverLoading" v-if="recoverStep === 0" class="rounded-login-buttons rounded-login-buttons-text" @click="sendRecoverMail">{{$t('message.sendEmailRecover')}}</v-btn>
				<v-btn id="v-step-button-confirm" :loading="recoverLoading" v-if="recoverStep === 1" class="rounded-login-buttons rounded-login-buttons-text" @click="validateRecoverToken">{{$t('message.validateRecoverToken')}}</v-btn>
				<v-btn id="v-step-button-change" :loading="recoverLoading" v-if="recoverStep === 2" class="rounded-login-buttons rounded-login-buttons-text" @click="resetAccountPassword">{{$t('message.recoverPasswordAccount')}}</v-btn>
			</v-flex>
			<v-flex xs8>
				<div class="text-xs-right">
					<v-btn flat dark @click="returnToAccess" class="rounded-login-buttons">
						<v-icon left>zmdi-arrow-left</v-icon>{{$t('message.backToLogin')}}
					</v-btn>
				</div>
			</v-flex>
		</v-layout>
		<v-layout v-if="recoverStep === 1" row wrap>
			<v-flex xs12>
				<p id="v-step-resend" class="mb-3 white--text pt-4">
					<vac :left-time="counter" ref="vac">
						<span class="subtitle-Card" slot="process" slot-scope="{ timeObj }">{{ $t('message.recoverWaitTime', {email:recoverEmail, counter:`${timeObj.m}:${timeObj.s}`})}}</span>
						<span class="subtitle-Card" slot="finish">{{$t('message.recoverTokenMsg')}}
							<a @click="sendRecoverMail" class="mb-3 white--text" style="text-decoration:underline !important"> {{$t('message.recoverTokenLink')}}</a></span>
					</vac>
				</p>
			</v-flex>
		</v-layout>
		<v-tour v-if="recoverStep===0" name="recoverTour" :steps="steps1" :options="tourOptions"></v-tour>
		<v-tour v-if="recoverStep===1" name="recoverTour" :steps="steps2" :options="tourOptions"></v-tour>
		<v-tour v-if="recoverStep===2" name="recoverTour" :steps="steps3" :options="tourOptions"></v-tour>
	</v-form>
	<v-layout row wrap align-center justify-center>
		<v-flex xs12>
			<div class="text-xs-center">
				<span class="pointer-hand" @click="startHelpTour">{{$t(' message.needHelp')}}</span>
			</div>
		</v-flex>
	</v-layout>
</div>
</template>
<script>
import Vue from 'vue';
import {
	mapGetters
} from "vuex";
import ERRORS from '../../constants/errors';
export default {
	props: {
		'currentTab': Number,
		'tabFunction': Number,
		'initialEmail': String,
	},
	computed: {
		...mapGetters(["currentBussinesClub"]),
	},
	watch: {
		currentTab() {
			this.clearForm();
		},
		tabFunction() {
			this.clearForm();
		},
		initialEmail(value) {
			if (this.currentTab === 1 && this.tabFunction === 2) {
				this.recoverEmail = this.initialEmail;
			}
		},
		recoverEmail() {
			this.recoverEmailHint = '';
		},
		recoverToken() {
			this.recoverTokenHint = '';
		},
	},
	data() {
		return {
			recoverLoading: false,
			recoverStep: 0,
			recoverEmail: null,
			recoverEmailRules: [],
			recoverEmailHint: '',
			recoverToken: null,
			recoverTokenRules: [],
			recoverTokenHint: '',
			recoverPassword: null,
			recoverPasswordRules: [],
			recoverPasswordShow: false,
			counter: 0,

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
			steps1: [{
					target: '#v-step-email',
					content: this.$t('message.tourRecoverEmail')
				},
				{
					target: '#v-step-button-send',
					content: this.$t('message.tourRecoverSendEmail')
				},
			],
			steps2: [{
					target: '#v-step-resend',
					content: this.$t('message.tourRecoverResend')
				},
				{
					target: '#v-step-token',
					content: this.$t('message.tourRecoverToken')
				},
				{
					target: '#v-step-button-confirm',
					content: this.$t('message.tourRecoverConfirmToken')
				},
			],
			steps3: [{
					target: '#v-step-password',
					content: this.$t('message.tourRecoverPassword')
				},
				{
					target: '#v-step-button-change',
					content: this.$t('message.tourRecoverChangePassword')
				},
			],
		};
	},
	created() {
		this.recoverEmailRules = [
			v => !!v || '* ' + this.$t('message.emailRequiredRuleMessage'),
			v => /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(v) || '* ' + this.$t('message.sessionEmailInvalid')
		];
		this.recoverTokenRules = [
			v => !!v || '* ' + this.$t('message.confirmationTokenRequired'),
			v => v && v.length === 8 || '* ' + this.$t('message.confirmationTokenInvalid')
		];
		this.recoverPasswordRules = [
			v => this.recoverStep < 2 || !!v || '* ' + this.$t('message.passwordRequired'),
			v => this.recoverStep < 2 || v && /(?=.*?[A-Z])(?=.*?[a-z])(?=.*?[0-9]).{8,}$/.test(v) || '* ' + this.$t('message.strengthPasswordWeak')
		];
	},
	methods: {
		/**
		 * Start the form help tour
		 */
		startHelpTour() {
			this.$tours['recoverTour'].start()
		},

		/**
		 * Clear the recover form
		 */
		clearForm() {
			this.recoverLoading = false;
			this.recoverStep = 0;
			this.recoverEmail = null;
			this.recoverEmailHint = '';
			this.recoverToken = null;
			this.recoverTokenHint = '';
			this.recoverPassword = null;
			this.recoverPasswordShow = false;
			if (this.$refs.recover) {
				this.$refs.recover.resetValidation();
				this.$refs.recover.reset();
			}
		},

		/**
		 * Send a recover account token to the given email address
		 */
		sendRecoverMail() {
			if (!this.$refs.recover.validate()) {
				return;
			}

			this.recoverLoading = true;
			this.$store.dispatch("doRequestPasswordRecovery", {
				email: this.recoverEmail
			}).then(recover => {
				this.$store.dispatch("doNotification", {
					msg: this.$t('message.sendTokenForValidation'),
					type: 'success'
				});
				this.recoverStep = 1;
				this.recoverToken = null;
				this.$refs.recover.resetValidation();

				/* Set an interval of 5 minutes */
				this.counter = 300000;
				Vue.nextTick(() => {
					this.$refs.vac.startCountdown(300000);
				});
			}).catch(err => {
				const error = Vue.getObjectPath(err, 'response.data.error', -1);
				switch (error) {
					case ERRORS.IAM.USER_NOT_FOUND:
						this.$store.dispatch("doNotification", {
							msg: this.$t('message.sessionUserNotFound'),
							type: 'error'
						});
						this.recoverEmailHint = '* ' + this.$t('message.sessionUserNotFound');
						break;
					case ERRORS.IAM.EMAIL_NOT_CONFIRMED:
						this.$store.dispatch("doNotification", {
							msg: this.$t('message.confirmAccountRequired'),
							type: 'error'
						});
						this.recoverEmailHint = '* ' + this.$t('message.confirmAccountRequired');
						break;

					default:
						this.$store.dispatch("doNotification", {
							msg: this.$t('message.sessionErrorDefault'),
							type: 'error'
						});
				}
			}).finally(() => {
				this.recoverLoading = false;
			});
		},

		/**
		 * Validate the user account recover token
		 */
		validateRecoverToken() {
			if (!this.$refs.recover.validate()) {
				return;
			}

			/* Validat the recover token */
			this.recoverLoading = true;
			this.$store.dispatch("doValidateRecoverToken", {
				email: this.recoverEmail,
				token: this.recoverToken
			}).then(validate => {
				if (validate.data.data.valid == true) {
					this.$store.dispatch("doNotification", {
						msg: this.$t('message.tokenValidatedForResetPsw'),
						type: 'success'
					});
					this.recoverStep = 2;
				} else {
					this.recoverTokenHint = '* ' + this.$t('message.sessionInvalidToken');
				}
			}).catch(err => {
				this.$store.dispatch("doNotification", {
					msg: this.$t('message.sessionInvalidToken'),
					type: 'error'
				});
			}).finally(() => {
				this.recoverLoading = false;
			});
		},

		/**
		 * Reset the user account password
		 */
		resetAccountPassword() {
			if (!this.$refs.recover.validate()) {
				return;
			}

			/* Change the password into the server */
			this.recoverLoading = true;
			this.$store.dispatch("doRecoverSetPassword", {
				email: this.recoverEmail,
				token: this.recoverToken,
				password: this.recoverPassword,
				appPayload: {
					business_club_id: this.currentBussinesClub.clubId
				},
			}).then(reset => {
				this.$store.dispatch("doNotification", {
					msg: this.$t('message.resetPasswordSuccessfully'),
					type: 'success'
				});
				this.clearForm();
				this.returnToAccess();
			}).catch(err => {
				const error = Vue.getObjectPath(err, 'response.data.error', -1);
				switch (error) {
					case ERRORS.IAM.INVALID_PASSWORD_REQUIREMENTS:
						this.registerPassword = "";
						this.$store.dispatch("doNotification", {
							msg: this.$t('message.sessionPasswordWeak'),
							type: 'error'
						});
						break;
					default:
						this.$store.dispatch("doNotification", {
							msg: this.$t('message.sessionPasswordWeak'),
							type: 'error'
						});
				}
			}).finally(() => {
				this.recoverLoading = false;
			});
		},

		/**
		 * Return to the access form
		 */
		returnToAccess() {
			this.clearInterval();
			this.$emit('returnAccess');
		},

		/**
		 * Clear the counter interval
		 */
		clearInterval() {
			this.counter = 0;
			if (this.$refs.vac) {
				this.$refs.vac.stopCountdown();
			}
		},
	}
};
</script>
