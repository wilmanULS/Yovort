<template>
<div v-if="currentTab === 1 && tabFunction===1 " class="animated fadeIn">
	<v-form onSubmit="return false;" class="light_field mb-1" ref="confirm" lazy-validation>
		<v-layout row wrap>
			<v-flex xs12>
				<v-text-field id="v-step-token" v-model="confirmToken" :rules="confirmTokenRules" autocomplete="nonex" :label="$t('message.validationTokenRegister')+'*'" solo background-color="light"></v-text-field>
			</v-flex>
		</v-layout>
		<v-layout row wrap>
			<v-flex xs4>
				<v-btn :loading="confirmProgress" class="rounded-login-buttons rounded-login-buttons-text" @click="confirmUserAccount">{{$t('message.validateRegister')}}</v-btn>
			</v-flex>
			<v-flex xs8>
				<div class="text-xs-right">
				<v-btn flat dark @click="returnToAccess" class="rounded-login-buttons">
					<v-icon left>zmdi-arrow-left</v-icon>{{$t('message.backToLogin')}}
				</v-btn>
				</div>
			</v-flex>
		</v-layout>
		<v-layout row wrap>
			<v-flex xs12>
				<p class="mb-3 white--text pt-4">
					<vac :left-time="counter" ref="vac">
						<span class="subtitle-Card" slot="process" slot-scope="{ timeObj }">{{ $t('message.recoverWaitTime', {email:confirmEmail, counter:`${timeObj.m}:${timeObj.s}`})}}</span>
						<span class="subtitle-Card" slot="finish">{{$t('message.recoverTokenMsg')}}
							<a id="v-step-resend" @click="resendConfirmationEmail" class="mb-3 white--text" style="text-decoration:underline !important"> {{$t('message.recoverTokenLink')}}</a></span>
					</vac>
				</p>
			</v-flex>
		</v-layout>
		<v-tour name="confirmTour" :steps="steps" :options="tourOptions"></v-tour>
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
		'confirmEmail': String,
		'confirmUserId': String,
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
		}
	},
	data() {
		return {
			confirmToken: null,
			confirmProgress: false,
			confirmTokenRules: [],
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
            steps: [{
                    target: '#v-step-token',
                    content: this.$t('message.tourConfirmToken')
                },
                {
                    target: '#v-step-resend',
                    content: this.$t('message.tourConfirmResend')
                },
            ],
		};
	},
	created() {
		this.confirmTokenRules = [
			v => !!v || '* ' + this.$t('message.confirmationTokenRequired'),
			v => v && v.length === 8 || '* ' + this.$t('message.confirmationTokenInvalid')
		];
	},
	mounted() {
		this.clearForm();
	},
	methods: {
		/**
         * Start the form help tour
         */
        startHelpTour() {
            this.$tours['confirmTour'].start()
        },

		/**
		 * Clear the signin form
		 */
		clearForm() {
			this.confirmProgress = false;
			this.confirmToken = null;
			if (this.$refs.confim) {
				this.clearInterval();
				this.$refs.confim.resetValidation();
				this.$refs.confirm.reset();
			}
		},

		/**
		 * This method sends an email to user who don't know its token for confirm account
		 **/
		resendConfirmationEmail() {
			this.$store.dispatch("doResendConfirmation", {
				email: this.confirmEmail,
				appPayload: {
					business_club_id: this.currentBussinesClub.clubId
				}
			}).then(() => {
				this.$store.dispatch("doNotification", {
					msg: this.$t('message.sendTokenForValidation'),
					type: 'success'
				});

				/* Set an interval of 5 minutes */
				this.counter = 300000;
				Vue.nextTick(() => {
					this.$refs.vac.startCountdown(300000);
				});
			}).catch(err => {
				const error = Vue.getObjectPath(err, 'response.data.error', -1)
				switch (error) {
					case ERRORS.IAM.USER_NOT_FOUND:
						this.$store.dispatch("doNotification", {
							msg: this.$t('message.emailUserConfirmNotFound'),
							type: 'error'
						});
						this.returnToAccess();
						break;

					default:
						this.$store.dispatch("doNotification", {
							msg: this.$t('message.sessionErrorDefault'),
							type: 'error'
						});
				}
			});
		},

		/**
		 * Confirm the user account with the given confirmation token
		 */
		confirmUserAccount() {
			/* Validate the form */
			if (!this.$refs.confirm.validate()) {
				return;
			}

			/*Validate token for confirmation user email*/
			this.confirmProgress = true;
			this.$store.dispatch("doConfirmAccount", {
				userId: this.confirmUserId,
				token: this.confirmToken,
				appPayload: {
					business_club_id: this.currentBussinesClub.clubId
				}
			}).then(validation => {
				this.$store.dispatch("doNotification", {
					msg: this.$t('message.sessionTokenValidated'),
					type: 'success'
				});
				this.clearInterval();
				this.returnToAccess();
			}).catch(err => {
				const error = Vue.getObjectPath(err, 'response.data.error', -1)
				switch (error) {
					case ERRORS.IAM.INVALID_TOKEN:
						this.$store.dispatch("doNotification", {
							msg: this.$t('message.sessionInvalidToken'),
							type: 'error'
						});
						break;
					default:
						this.$store.dispatch("doNotification", {
							msg: this.$t('message.sessionErrorDefault'),
							type: 'error'
						});
				}
			}).finally(() => {
				this.clearForm();
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
			this.$refs.vac.stopCountdown();
		}
	}
};
</script>
