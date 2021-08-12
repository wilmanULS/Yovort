<template>
<div v-if="currentTab === 0" class="animated fadeIn">
	<v-form onSubmit="return false;" class="light_field mb-1" ref="signin" lazy-validation>
		<v-text-field id="v-step-email" browser-autocomplete="off" :rules="loginEmailRules" v-model="loginEmail" type="email" :label="$t('message.sessionEmail')+'*'" solo background-color="light" flat></v-text-field>
		<v-text-field id="v-step-password" browser-autocomplete="new-password" :rules="loginPasswordRules" v-model="loginPassword" :color="currentBussinesClub.theme.primary" flat :label="$t('message.sessionPassword')+'*'" @keyup.enter="execLogin"
		 solo background-color="light" @click:append="loginShowPass = !loginShowPass" :type="loginShowPass ? 'text' : 'password'" :append-icon="loginShowPass ? 'mdi mdi-eye' : 'mid mdi-eye-off'"></v-text-field>
		<v-layout row wrap>
			<v-flex xs4>
				<v-btn :loading="loginProgress" class="rounded-login-buttons rounded-login-buttons-text" @click="execLogin()">{{$t('message.sessionLogIn')}}</v-btn>
			</v-flex>
			<v-flex xs8>
				<div class="text-xs-right">
					<v-btn id="v-step-recover" class="rounded-login-buttons" flat dark @click="recoverPassword">{{$t('message.sessionForgotPasword')}}</v-btn>
				</div>
			</v-flex>
		</v-layout>
		<v-tour name="loginTour" :steps="steps" :options="tourOptions"></v-tour>
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
import Vue from "vue";
import {
	mapGetters
} from "vuex";
import router from "../../router";
import {
	EventBus
} from "../../eventBus.js";
import ERRORS from "../../constants/errors";
import AppConfig from "../../constants/AppConfig";

export default {
	props: {
		currentTab: Number
	},
	computed: {
		...mapGetters([
			"currentBussinesClub",
			"userId",
			"userCredential",
			"userCredentialRefresh"
		])
	},
	watch: {
		currentTab() {
			this.clearForm();
		}
	},
	data() {
		return {
			loginEmail: "",
			loginPassword: "",
			loginEmailRules: [],
			loginPasswordRules: [],
			loginShowPass: false,
			loginProgress: false,
			tmpEmail: null,

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
					target: '#v-step-email',
					content: this.$t('message.tourLoginEmail')
				},
				{
					target: '#v-step-password',
					content: this.$t('message.tourLoginPassword')
				},
				{
					target: '#v-step-recover',
					content: this.$t('message.tourLoginRecover')
				},
			],
		};
	},
	created() {
		this.loginEmailRules = [
			v => !!v || "* " + this.$t("message.emailRequiredRuleMessage"),
			v =>
			/^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(v) ||
			"* " + this.$t("message.sessionEmailInvalid")
		];
		this.loginPasswordRules = [
			v => !!v || "* " + this.$t("message.passwordRequired")
		];
	},
	mounted() {
		this.clearForm();

		/*
		 * This triggered when an user is not a member of current business club and want to join as member.
		 */
		EventBus.$on("joinAndRedirect", data => {
			this.loadingLogin = false;
			this.$store
				.dispatch("requestCreateMembership", {
					email: this.tmpEmail,
					business_club_id: this.currentBussinesClub.clubId
				})
				.then(res => {
					EventBus.$emit("openUniversalDialog", {
						text: this.$t("message.requestMembershipDescription"),
						title: this.$t("message.requestMembership"),
						hasCloseBtn: true,
						type: "",
						redirectAuth: true
					});
				})
				.catch(err => {
					const error = Vue.getObjectPath(err, "response.data.error", -1);
					switch (error) {
						default:
							this.$store.dispatch("doLogoutUser");
					}
				});
		});
		//This triggered when an user accepted the membership 
			EventBus.$on("joinMemberAndRedirect", data => {
				this.$store.dispatch("agregateMember",this.userId).then(res=>{
					var errorKey=res.data.data.error
					switch (errorKey) {
						      case 0: this.$store.dispatch("doNotification", {
								  msg: this.$t("message.memberHasBeenAdded"),
								  type: "success"
								  })
             				 break;
          					  case 1:
            				  this.$store.dispatch("doNotification", {
            				    msg: this.$t("message.errorFindClub"),
            				    type: "error"
           				   });
             				 break;
           					 case 2:
           					   this.$store.dispatch("doNotification", {
           					     msg: this.$t("message.memberDontExist"),
           					     type: "error"
           					   });
           					   break;
           					 case 3:
           					   this.$store.dispatch("doNotification", {
           					     msg: this.$t("message.unknownErrorLoadingApp"),
           					     type: "error"
          					    });
           					   break;
					}
				})
		});

		/*
		 * This triggered when an user is goint to be redirect to Yovirt
		 */
		EventBus.$on("redirectToYovirt", data => {
			if (data) {
				const userCredential = this.userCredential;
				const userCredentialRefresh = this.userCredentialRefresh;
				const userId = this.userId;
				window.location =
					`https://www.${AppConfig.BASE_URL}` +
					"/session/login?VATUP=" +
					userCredential +
					"&VRTUP=" +
					userCredentialRefresh +
					"&userId=" +
					userId;
			} else {
				/*this.$store.dispatch("doNotification", {
				  msg: this.$t("message.sessionErrorDefault"),
				  type: "error"
				}); */
				this.$store.dispatch("doLogoutUser");
			}
		});
	},
	methods: {
		/**
		 * Start the form help tour
		 */
		startHelpTour() {
			this.$tours['loginTour'].start()
		},

		/**
		 * Clear the signin form
		 */
		clearForm() {
			this.loginProgress = false;
			this.loginEmail = null;
			this.loginPassword = null;
			if (this.$refs.signin) {
				this.$refs.signin.resetValidation();
				this.$refs.signin.reset();
			}
		},

		/**
		 * Authenticate an user account
		 */
		execLogin() {
			/* Validate the form */
			if (!this.$refs.signin.validate()) {
				return;
			}

			/*Try to do login into app*/
			this.tmpEmail = this.loginEmail;
			this.loginProgress = true;
			this.$store
				.dispatch("doUserAuthentication", {
					username: this.loginEmail,
					password: this.loginPassword
				})
				.then(() => {
					/*User is authenticated succesfully*/
					let searchMembership = {
						business_club_id: this.currentBussinesClub.clubId
					};

					/*Get the state of membership into this business club for current user*/
					this.$store
						.dispatch("getMembershipState", searchMembership)
						.then(membership => {
							if (Vue.getObjectPath(membership, "data.data.exists", false)) {
								const membershipValue = Vue.getObjectPath(
									membership,
									"data.data.membership.0.membership.status_flag_catalog",
									-1
								);
								const rejectedCause = Vue.getObjectPath(
									membership,
									"data.data.membership.0.membership.rejected_cause",
									null
								);

								switch (membershipValue) {
									case 0:
										/* Membership pending to approval */
										EventBus.$emit("openUniversalDialog", {
											text: this.$t("message.requestMembershipDescription"),
											title: this.$t("message.requestMembership"),
											hasCloseBtn: true,
											type: "",
											redirectAuth: true
										});
										break;
									case 2:
										/* Connect Chat*/
										this.$store.dispatch("connectChat", sessionStorage.getItem("VATUP"))
										/* Membership approved */
										router.push("/account/profile");
										break;

									case 4:
										/* Membership not approved */

										if (rejectedCause) {
											let ONE_DAY = 1000 * 60 * 60 * 24;
											/* Get last item of rejected_cause array */
											let rejectedMembership = membership.data.data.membership[0].membership.rejected_cause.slice(-1)[0];
											let days_unavailable = rejectedMembership.days_unavailable;
											let createdDate = new Date(rejectedMembership.createdAt);

											/* Calculating days remaining until user can request an membership into business club */
											createdDate.setDate(createdDate.getDate() + days_unavailable);
											let startDate = createdDate.getTime();
											let currentDate = new Date().getTime();
											let difference_ms = Math.abs(startDate - currentDate);
											let days_remaining = Math.round(difference_ms / ONE_DAY);

											/* If time rejected has expired then user can request an membership */
											if (days_remaining <= 0) {
												EventBus.$emit("openUniversalDialog", {
													text: this.$t("message.userJoinOnBusiness"),
													title: this.$t("message.requestToJoin"),
													hasCloseBtn: true,
													type: "requestJoin",
													redirectAuth: false
												});
											} else {
												/* If time rejected is currently active then user is notified and redirect to Yovirt Business Club  */

												EventBus.$emit("openUniversalDialog", {
													text: this.$t("message.rejectMembershipActive") +
														days_remaining +
														this.$t(
															"message.rejectMemberMembershipDaysRemaining"
														),
													title: this.$t("message.rejectMembership"),
													hasCloseBtn: true,
													type: "",
													redirectAuth: true
												});
											}
										} else {
											EventBus.$emit("openUniversalDialog", {
												text: this.$t("message.rejectMembershipDescription"),
												title: this.$t("message.rejectMembership"),
												hasCloseBtn: true,
												type: "",
												redirectAuth: true
											});
										}
										break;

									case 6:
										/* User blocked from business club */
										this.$store.dispatch("doNotification", {
											msg: "Usuario bloqueado",
											type: "error"
										});

										break;
									case 8:
										/* User invited to business club */
										EventBus.$emit("openUniversalDialog", {
										text: this.$t("message.membershipInvitedDescription"),
										title: this.$t("message.membershipInvited"),
										hasCloseBtn: true,
										type: "joinMember",
										redirectAuth: false
											}); 
										break;

									default:
										/* Other */
										this.$store.dispatch("doNotification", {
											msg: this.$t("message.sessionErrorDefault"),
											type: "error"
										});
										this.$store.dispatch("doLogoutUser");
										break;
								}
							} else {
								/* The user is not member of the business club */
								if(Vue.getObjectPath(membership, "data.data.settingsClub", null)){
								//setting key para invitacion(1), solicitud(2) o ambas(3)
									var settingKey=Vue.getObjectPath(membership, "data.data.settingsClub")
									switch (settingKey) {
										case 1:
										EventBus.$emit("openUniversalDialog", {
										text: this.$t("message.membershipNotFoundDescriptionByAgregate"),
										title: this.$t("message.membershipNotFound"),
										hasCloseBtn: false,
										type: "standar",
										redirectAuth: true
											}); 
										break;
										case 2:
										EventBus.$emit("openUniversalDialog", {
										text: this.$t("message.membershipNotFoundDescription"),
										title: this.$t("message.membershipNotFound"),
										hasCloseBtn: true,
										type: "requestJoin",
										redirectAuth: false
								}); 
										break;
										case 3:
										EventBus.$emit("openUniversalDialog", {
										text: this.$t("message.membershipNotFoundDescription"),
										title: this.$t("message.membershipNotFound"),
										hasCloseBtn: true,
										type: "requestJoin",
										redirectAuth: false
								}); 
										break;
											}
									}
							/* 	EventBus.$emit("openUniversalDialog", {
									text: this.$t("message.membershipNotFoundDescription"),
									title: this.$t("message.membershipNotFound"),
									hasCloseBtn: true,
									type: "requestJoin",
									redirectAuth: false
								}); */
							}
						})
						.catch(() => {
							this.$store.dispatch("doNotification", {
								msg: this.$t("message.sessionErrorDefault"),
								type: "error"
							});
						});
				})
				.catch(err => {
					const error = Vue.getObjectPath(err, "response.data.error", -1);
					switch (error) {
						case ERRORS.IAM.INVALID_PASSWORD:
						case ERRORS.IAM.USER_NOT_FOUND:
							this.$store.dispatch("doNotification", {
								msg: this.$t("message.invalidLogin"),
								type: "error"
							});
							break;

						case ERRORS.IAM.EMAIL_NOT_CONFIRMED:
							this.$store.dispatch("doNotification", {
								msg: this.$t("message.sessionUserMustConfirm"),
								type: "error"
							});
							this.confirmAccount(
								Vue.getObjectPath(err, "response.data.data.userId", null)
							);
							break;

						case ERRORS.IAM.USER_BLOCKED:
							this.$store.dispatch("doNotification", {
								msg: this.$t("message.sessionUserBlocked"),
								type: "error"
							});
							break;

						default:
							this.$store.dispatch("doNotification", {
								msg: this.$t("message.sessionErrorDefault"),
								type: "error"
							});
					}
					this.password = "";
				})
				.finally(() => {
					this.clearForm();
				});
		},

		/**
		 * Request a recover password
		 */
		recoverPassword() {
			this.$emit("recover", {
				email: this.loginEmail
			});
		},

		/**
		 * Show the confirmation view because user need to confirm the account
		 */
		confirmAccount(userId) {
			this.$emit("confirmAccount", {
				email: this.loginEmail,
				userId: userId
			});
		}
	}
};
</script>
