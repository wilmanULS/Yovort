<template>
<div>
	<y-spacer></y-spacer>
	<v-container ma-0 pa-0 grid-list-{xs through xl}>
		<v-layout class="bg-color-grey pa-0" row wrap>
			<v-flex xs12 sm12 md4 lg3>
				<v-card class="border-1px bg-color-grey" ml-5 mr-5 pl-5 pr-5 pt-0 mt-0 pb-0 mb-0 flat>
					<v-layout row wrap>
						<v-flex class="pt-1" xs12>
							<v-layout px-2 row wrap>
								<v-flex class="px-2">
								</v-flex>
								<v-flex class="px-2">
									<v-chart :options="pie" style="width: 260px; height:260px;"></v-chart>
								</v-flex>
								<v-flex class="px-2">
								</v-flex>
							</v-layout>
							<v-layout row wrap class="px-3 pt-3 pb-1">
								<v-flex class="py-0 px-3 darker-titles" xs10 style="border: 1px dashed">
									<v-layout class="pt-0" align-center justify-center row fill-height>
										<span v-if="userProfile" class="darker-titles" style="font-size:17pt;">{{userProfile.personal_info.vCode}}</span>
									</v-layout>
								</v-flex>
								<v-flex xs2 class="cardLeft">
									<v-layout class="pt-0" align-center justify-center row fill-height>
										<v-icon v-if="userProfile" v-clipboard:copy="userProfile.personal_info.vCode" v-clipboard:success="successVcodeCopy" :color="currentBussinesClub.theme.primary" style="font-size:20pt;cursor:pointer">file_copy
										</v-icon>
									</v-layout>
								</v-flex>
								<v-flex xs12>
									<v-layout pt-2 align-center justify-center row fill-height>
										<v-btn v-if="userProfile" :href="webUrl+'/session/login?ref='+userProfile.personal_info.vCode" target="_blank" small block :color="currentBussinesClub.theme.primary" dark>{{$t('message.goToInvitedsURL')}}
										</v-btn>
									</v-layout>
								</v-flex>
								<v-flex xs12>
									<v-layout pt-2 align-center justify-center row fill-height>
										<v-btn v-if="userProfile" v-clipboard:copy="webUrl + '?ref=' + getObjectPath(userProfile,'personal_info.vCode','')" v-clipboard:success="successfullCopy" target="_blank" small block
										 :color="currentBussinesClub.theme.primary" dark>{{$t('message.copyReferrealLink')}}
										</v-btn>
									</v-layout>
								</v-flex>
								<v-flex xs12 class="pt-1">
									<v-layout align-center justify-center row fill-height>
										<v-btn @click="openInvitedDialog()" block small :color="currentBussinesClub.theme.primary" dark>{{$t('message.inviteFriend')}}</v-btn>
									</v-layout>
								</v-flex>
								<v-flex xs12>
									<v-flex xs12 class="pt-4" style="text-align: center;">
										<span class="noselect" style="color:gray; font-size:11pt">{{$t('message.socialShareMessage')}}</span>
									</v-flex>
									<v-layout row wrap mt-3>
										<v-flex xs4 style="text-align: center">
											<div :data-href="webUrl+'/?ref='+userProfile.personal_info.vCode" data-layout="button">
												<a :style="'color:'+currentBussinesClub.theme.primary" target="_blank"
												 :href="'https://www.facebook.com/sharer/sharer.php?u='+webUrl+'/session/login?ref='+userProfile.personal_info.vCode+'&src=sdkpreparse'">
													<i class="mdi mdi-36px mdi-facebook"></i>
												</a>
											</div>
										</v-flex>
										<v-flex xs4 style="text-align: center">
											<div>
												<a :style="'color:'+currentBussinesClub.theme.primary" target="_blank" class="twitter-share-button"
												 :href="'https://twitter.com/intent/tweet?text='+$t('message.socialInvitedText')+' '+webUrl+'/?ref='+userProfile.personal_info.vCode">
													<i class="mdi mdi-36px mdi-twitter"></i>
												</a>
											</div>
										</v-flex>
										<v-flex xs4 style="text-align: center">
											<div :data-href="webUrl+'/?ref='+userProfile.personal_info.vCode" data-layout="button">
												<a class="hidden-md-and-up" :style="'color:'+currentBussinesClub.theme.primary" target="_blank"
												 :href="'whatsapp://send?text='+$t('message.socialInvitedText')+' '+webUrl+'/?ref='+userProfile.personal_info.vCode">
													<i class="mdi mdi-36px mdi-whatsapp"></i>
												</a>
												<a class="hidden-sm-and-down" :style="'color:'+currentBussinesClub.theme.primary" target="_blank"
												 :href="'https://web.whatsapp.com/send?text='+$t('message.socialInvitedText')+' '+webUrl+'/?ref'+'%3D'+userProfile.personal_info.vCode" data-original-title="whatsapp" rel="tooltip" data-placement="left"
												 data-action="share/whatsapp/share">
													<i class="mdi mdi-36px mdi-whatsapp"></i>
												</a>
											</div>
										</v-flex>
									</v-layout>
								</v-flex>
							</v-layout>
						</v-flex>
					</v-layout>
				</v-card>
			</v-flex>
			<v-flex class="padding-4" xs12 sm12 md8 lg9>
				<y-spacer v-if="$vuetify.breakpoint.xsOnly"></y-spacer>
				<v-card flat class="bg-color-grey" pt-0 mt-0>
					<v-layout ma-0 pa-0 row wrap>
						<v-flex xs12 lg12 xl12 class="bg-color-grey">
							<v-layout pl-1 align-center row fill-height>
								<span class="titleDivider noselect" v-bind:style="{'color':currentBussinesClub.theme.primary}">{{$t('message.myGuests')}}</span>
							</v-layout>
							<v-divider class="ml-1 mr-1 mx-0 pa-0" :style="'border:0.7px '+currentBussinesClub.theme.primary+' solid;border-color:'+currentBussinesClub.theme.primary+'!important'"></v-divider>
						</v-flex>
						<v-flex class="bg-color-grey" xs12 hidden-lg-and-up>
							<v-layout pl-2 align-left justify-end row wrap>
								<v-flex pt-1 xs11 style="height: 32px;">
									<v-text-field style="height: 32px;" :placeholder="$t('message.search')" class="pb-0 mb-0 pt-0 mt-0" hide-details :color="currentBussinesClub.theme.primary" single-line v-model="searchValue" append-icon="search"
									 @keyup="search"></v-text-field>
								</v-flex>
								<v-flex xs1>
									<v-menu bottom offset-y>
										<v-btn icon slot="activator">
											<v-icon>more_vert</v-icon>
										</v-btn>
										<v-list>
											<v-list-tile @click="orderBy(1)">
												<i class="fas fa-arrow-up"></i>&nbsp;
												<v-list-tile-title>{{$t('message.orderByLessReferreds')}}</v-list-tile-title>
											</v-list-tile>
											<v-list-tile @click="orderBy(2)">
												<i class="fas fa-arrow-down"></i>&nbsp;
												<v-list-tile-title>{{$t('message.orderByMoreReferreds')}}</v-list-tile-title>
											</v-list-tile>
											<v-list-tile @click="orderBy(3)">
												<i class="fas fa-sort-alpha-down"></i>&nbsp;
												<v-list-tile-title>{{$t('message.orderByAplhabeticallyAsc')}}</v-list-tile-title>
											</v-list-tile>
											<v-list-tile @click="orderBy(4)">
												<i class="fas fa-sort-alpha-up"></i>&nbsp;
												<v-list-tile-title>{{$t('message.orderByAplhabeticallyDesc')}}</v-list-tile-title>
											</v-list-tile>
										</v-list>
									</v-menu>
								</v-flex>
							</v-layout>
						</v-flex>
					</v-layout>
					<v-tabs v-if="listReferralsFront&&listReferralsFront['l1']!=null" show-arrows :slider-color="currentBussinesClub.theme.primary" pt-0>
						<v-layout class="bg-color-grey">
							<v-tab class="bg-color-grey" :ripple="false" v-for="(level,i) in listReferralsFront " :key="i">{{$t('message.level')}} {{ i.replace('l','')}}</v-tab>
						</v-layout>
						<v-flex class="bg-color-grey" xs8 lg5 xl5 hidden-md-and-down>
							<v-layout pr-3 align-left justify-end row wrap>
								<v-flex pt-1 xs6 lg9 style="height: 32px;">
									<v-text-field style="height: 32px;" :placeholder="$t('message.search')" class="pb-0 mb-0 pt-0 mt-0" hide-details single-line :color="currentBussinesClub.theme.primary" v-model="searchValue" append-icon="search"
									 @keyup="search"></v-text-field>
								</v-flex>
								<v-flex xs2 lg1>
									<v-menu bottom offset-y>
										<v-btn icon slot="activator">
											<v-icon>more_vert</v-icon>
										</v-btn>
										<v-list>
											<v-list-tile @click="orderBy(1)">
												<i class="fas fa-arrow-up"></i>&nbsp;
												<v-list-tile-title>{{$t('message.orderByLessReferreds')}}</v-list-tile-title>
											</v-list-tile>
											<v-list-tile @click="orderBy(2)">
												<i class="fas fa-arrow-down"></i>&nbsp;
												<v-list-tile-title>{{$t('message.orderByMoreReferreds')}}</v-list-tile-title>
											</v-list-tile>
											<v-list-tile @click="orderBy(3)">
												<i class="fas fa-sort-alpha-down"></i>&nbsp;
												<v-list-tile-title>{{$t('message.orderByAplhabeticallyAsc')}}</v-list-tile-title>
											</v-list-tile>
											<v-list-tile @click="orderBy(4)">
												<i class="fas fa-sort-alpha-up"></i>&nbsp;
												<v-list-tile-title>{{$t('message.orderByAplhabeticallyDesc')}}</v-list-tile-title>
											</v-list-tile>
										</v-list>
									</v-menu>
								</v-flex>
							</v-layout>
						</v-flex>
						<v-tabs-items>
							<v-tab-item :transition="false" :reverse-transition="false" v-for="(levelRefs,j) in listReferralsFront" :key="j">
								<vue-perfect-scrollbar class="container border-1px bg-color-grey">
									<div v-if="$vuetify.breakpoint.mdAndUp" class="content-x fill-vertical"
									 :style="levelRefs.length>4?'width:'+(((Math.round(levelRefs.length/4))+1)*105)+'px;':'width:'+(((levelRefs.length/4))*105)+'px;border:1px transparent solid'">
										<div v-for="(referred,k) in levelRefs" :key="k" style="width:105px;float: left;padding-bottom:30px">
											<center>
												<v-avatar style="cursor:pointer" :size="63" :color="currentBussinesClub.theme.primary" @click="openDialogInformation(referred.user_id)">
													<v-img :ref="'avatar'+j+''+k" :src="`${FMS_URL}/image/fetch/${referred.picture}?tx=resize(w:64,h:64)&username=${referred.name}&color=${referred.color}&size=64`"
													 :lazy-src="'/static/img/perfil_yovirt.png'" />
													<div v-if="referred.referredCountN1>0" class="customBadge" :style="'background-color:'+ currentBussinesClub.theme.secondary">
														<span class="noselect" style="z-indez:12!important;color:white" slot="badge">
															{{referred.referredCountN1}}
														</span>
													</div>
												</v-avatar>
												<v-spacer></v-spacer>
												<div style="max-width:140px" class="cut-text">
													<span class="noselect darker-titles" style="font-size:10pt;">{{referred.name}}</span>
												</div>
											</center>
										</div>
									</div>
									<div v-if="$vuetify.breakpoint.smAndDown">
										<div v-for="(referred,k) in levelRefs" :key="k" style="width:105px;float: left;padding-bottom:30px">
											<center>
												<v-avatar style="cursor:pointer" :size="63" :color="currentBussinesClub.theme.primary" @click="openDialogInformation(referred.user_id)">
													<v-img :ref="'avatar'+j+''+k" :src="`${FMS_URL}/image/fetch/${referred.picture}?tx=resize(w:64,h:64)&username=${referred.name}&color=${referred.color}&size=64`"
													 :lazy-src="'/static/img/perfil_yovirt.png'" />
													<div v-if="referred.referredCountN1>0" class="customBadge" :style="'background-color:'+ currentBussinesClub.theme.secondary">
														<span class="noselect" style="z-indez:12!important;color:white" slot="badge">
															{{referred.referredCountN1}}11
														</span>
													</div>
												</v-avatar>
												<v-spacer></v-spacer>
												<div style="max-width:140px" class="cut-text">
													<span class="noselect darker-titles" style="font-size:10pt;">{{referred.name}}</span>
												</div>
											</center>
										</div>
									</div>
								</vue-perfect-scrollbar>
							</v-tab-item>
						</v-tabs-items>
					</v-tabs>
					<v-container v-else grid-list-xs>
						<v-layout style="height:58vh" align-center justify-center row fill-height>
							<span>{{$t('message.dontHaveGuest')}}</span>
						</v-layout>
					</v-container>
				</v-card>
			</v-flex>
		</v-layout>
	</v-container>
	<v-details-dialog v-if="selectedUserProfile" titleToolbar="Información Contacto" ref="informationDialog" @onCancel="closeInformationDialog()"></v-details-dialog>
	<v-dialog v-model="invitedDialog" max-width="500px" persistent transition="dialog-bottom-transition">
		<v-card>
			<v-card-actions class="linear-gradient" dense primary-title :style="'color:white; padding:0px;'">
				<v-toolbar-title class="ml-3">{{$t('message.inviteFriend')}}</v-toolbar-title>
				<v-spacer></v-spacer>
				<v-btn icon dark @click="invitedDialog = false" class="mr-2">
					<v-icon>close</v-icon>
				</v-btn>
			</v-card-actions>
			<v-container grid-list-xs>
				<v-layout row wrap>
					<v-flex xs12>
						<v-form onSubmit="return false;" v-model="formInvitedValid">
							<v-text-field :label="$t('message.email')" v-model="inputInvitedEmail" :rules="[rules.email,rules.required]" ref="emailAdd"></v-text-field>
						</v-form>
					</v-flex>
				</v-layout>
			</v-container>
			<v-card-actions>
				<v-spacer></v-spacer>
				<v-btn flat :disabled="!formInvitedValid||sendingMail" @click="sendInvitationMail()" :color="currentBussinesClub.theme.primary" :loading="sendingMail">{{$t('message.invite')}}</v-btn>
			</v-card-actions>
		</v-card>
	</v-dialog>
</div>
</template>
<script>
import Vue from 'vue';
import PerfectScrollbar from "perfect-scrollbar";
import DetailsDialog from "./detailsDialog";
import AppConfig from "Constants/AppConfig.js";
import {
	mapGetters
} from "vuex";
export default {
	computed: {
		...mapGetters([
			"selectedUserProfile",
			"listReferrals",
			"currentBussinesClub"
		]),
	},
	props: {
		userProfile: {
			type: Object,
			required: true
		}
	},
	components: {
		"v-details-dialog": DetailsDialog
	},
	mounted() {
		this.$store.dispatch("getListReferrals", this.userProfile._id).then(res => {
			for (var level in this.listReferrals) {
				this.listReferralsFront[level] = this.listReferrals[level];
			}

			this.$nextTick(() => {
				/*	for (var level in this.listReferrals) {
						var element = document.getElementById("scrollBar" + level);
						new PerfectScrollbar(element, {
							useBothWheelAxes: true
						});
					}*/
			});
		});
	},
	data() {
		return {
			FMS_URL: AppConfig.FMS_URL,
			getObjectPath: Vue.getObjectPath,
			donutData: {
				Blueberry: 44,
				Strawberry: 23
			},
			webUrl: `https://${AppConfig.SUBDOMAIN_URL}.${AppConfig.BASE_URL}`,
			listReferralsFront: {
				l1: null
			},
			searchValue: "",
			sendingMail: false,
			validatorEmailState: true,
			formInvitedValid: false,
			inputInvitedEmail: "",
			invitedEmails: [],
			comboRule: [v => (v && v.length > 0) || "Campo Requerido"],
			vCode: null,
			invitedDialog: false,
			dataLineChart: "",
			Semanal: "",
			Mensual: "gray",
			Anual: "gray",
			SemanalText: "white",
			MensualText: "gray",
			AnualText: "gray",
			dialogInvite: false,
			//////////////////////
			valueDeterminate: 0,
			chartData: {},
			options2: {
				responsive: true,
				aspectRatio: 16 / 9
			},
			rules: {
				required: value => !!value || "Campo Requerido",
				email: value => {
					const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
					return pattern.test(value) || "Dirección email invalida";
				}
			},
			pie: {},
		};
	},
	created() {
		this.dataLineChart = [
			[new Date(), 5],
			[1368174456, 4],
			["2017-01-01 00:00:00 UTC", 7]
		];
		this.Semanal = this.currentBussinesClub.theme.primary;
		this.pie = {
			backgroundColor: "transparent",
			tooltip: {
				trigger: "item",
				formatter: "{a} <br/>{b} : {c} ({d}%)"
			},
			color: [
				this.currentBussinesClub.theme.primary,
				this.currentBussinesClub.theme.secondary,
			],
			series: [{
				name: this.$t("message.myReferredUsers", {
					count: this.userProfile.referredCount
				}),
				type: "pie",
				radius: ["50%", "70%"],
				avoidLabelOverlap: true,
				data: [{
						value: 0,
						name: this.$t("message.actives")
					},
					{
						value: this.userProfile.referredCount,
						name: this.$t("message.inactivesReferred")
					}
				],
				label: {
					normal: {
						show: true,
						position: "center"
					},
					emphasis: {
						show: true,
						textStyle: {
							fontSize: "20",
							fontWeight: "bold"
						}
					}
				},
				labelLine: {
					normal: {
						show: true
					}
				}
			}]
		};
	},
	methods: {
		successVcodeCopy() {
			this.$store.dispatch("doNotification", {
				msg: this.$t("message.referredVCodeCopied"),
				type: "success"
			});
		},
		successfullCopy() {
			this.$store.dispatch("doNotification", {
				msg: this.$t("message.referredLinkCopied"),
				type: "success"
			});
		},
		closeInformationDialog() {
			this.$refs.informationDialog.close();
		},
		openDialogInformation(userId) {
			this.$root.$children[0].$emit(
				"showLoading",
				"Cargando perfil de referido.."
			);
			this.$store.dispatch("getUserProfileById", userId).then(res => {
				this.$refs.informationDialog.openDialog();
				this.$root.$children[0].$emit("hideLoading");
			});
		},
		openDialog() {
			this.dialogInvite = true;
		},
		closeDialog() {
			this.dialogInvite = false;
		},
		removeTag(item) {
			this.invitedEmails.splice(this.invitedEmails.indexOf(item), 1);
		},
		openInvitedDialog() {
			this.invitedDialog = true;
			this.inputInvitedEmail = "";
			this.$refs.emailAdd.resetValidation();
			this.invitedEmails = [];
		},
		addInvitedEmail() {
			if (
				/^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
					this.inputInvitedEmail
				)
			) {
				var repeatEmail = this.invitedEmails.filter(
					a => a == this.inputInvitedEmail
				);
				if (!repeatEmail.length > 0) {
					this.invitedEmails.push(this.inputInvitedEmail);
					this.inputInvitedEmail = "";
					this.$refs.emailAdd.resetValidation();
				} else {
					this.inputInvitedEmail = "";
					this.$refs.emailAdd.resetValidation();
					this.$store.dispatch("doNotification", {
						msg: "Email ya registrado para invitación",
						type: "warning"
					});
				}
			} else {
				this.$store.dispatch("doNotification", {
					msg: "Por favor, ingrese un correo válido",
					type: "warning"
				});
			}
		},
		sendInvitationMail() {
			this.sendingMail = true;
			this.$store
				.dispatch("sendInvitationMail", {
					userId: this.userProfile._id,
					email: this.inputInvitedEmail
				})
				.then(res => {
					if (res.error == 0) {
						this.$store.dispatch("doNotification", {
							msg: "Invitación enviada con éxito",
							type: "success"
						});
						this.sendingMail = false;
						this.invitedDialog = false;
					}
					if (res.error == 1) {
						this.$store.dispatch("doNotification", {
							msg: "El usuario que intenta invitar no tiene perfil de usuario valido",
							type: "warning"
						});
						this.inputInvitedEmail = "";
						this.sendingMail = false;
					}
					if (res.error == 2) {
						this.$store.dispatch("doNotification", {
							msg: "El usuario que intenta invitar aun no ha confirmado su cuenta",
							type: "warning"
						});
						this.inputInvitedEmail = "";
						this.sendingMail = false;
					}
					if (res.error == 3) {
						this.$store.dispatch("doNotification", {
							msg: "El email al que intenta invitar ya esta registrado",
							type: "warning"
						});
						this.inputInvitedEmail = "";
						this.sendingMail = false;
					}
				});
		},
		search() {
			for (var level in this.listReferrals) {
				let auxItems = [];
				for (let i = 0; i < this.listReferrals[level].length; i++) {
					if (
						this.listReferrals[level][i].name
						.toUpperCase()
						.indexOf(this.searchValue.toUpperCase()) >= 0
					) {
						auxItems.push(this.listReferrals[level][i]);
					}
				}
				this.listReferralsFront[level] = auxItems;
			}
		},
		orderBy(command) {
			for (var level in this.listReferralsFront) {
				switch (command) {
					case 1:
						this.listReferralsFront[level].sort(function(a, b) {
							return a.referredCountN1 > b.referredCountN1 ?
								0 :
								a.referredCountN1 ?
								-1 :
								-1;
						});
						break;
					case 2:
						this.listReferralsFront[level].sort(function(a, b) {
							return a.referredCountN1 < b.referredCountN1 ?
								0 :
								a.referredCountN1 ?
								-1 :
								1;
						});
						break;

					case 3:
						this.listReferralsFront[level].sort(function(a, b) {
							return a.name.toUpperCase() > b.name.toUpperCase() ?
								0 :
								a.name ?
								-1 :
								1;
						});
						break;

					case 4:
						this.listReferralsFront[level].sort(function(a, b) {
							return a.name.toUpperCase() < b.name.toUpperCase() ?
								0 :
								a.name ?
								-1 :
								-1;
						});

						break;
				}
			}
		}
	},
	watch: {}
};
</script>
<style scoped>
.scroll-area {
	position: relative;
	margin: auto;
	width: auto;
	height: 300px;
	overflow: auto;
}

.leftTitle {
	padding-left: 30px;
}

.cardLeft {
	padding-left: 10px;
}

.cardsWH {
	width: 275px !important;
	height: 125px !important;
}

.guest-flex-padding {
	padding: 0px 5px 0px 0px;
}

.pading-20px {
	padding-left: 20px;
}

.custom-icon-purple {
	cursor: pointer;
	display: flex;
	width: 40px;
	height: 40px;
	color: #fff;
	font-size: 20pt;
	border-radius: 50% 50%;
	overflow: hidden;
	background-size: cover;
	border: 4px solid #fff;
	background-color: #82459a;
	opacity: 1;
}

.custom-icon-pink {
	cursor: pointer;
	display: flex;
	width: 40px;
	height: 40px;
	color: #fff;
	font-size: 20pt;
	border-radius: 50%;
	overflow: hidden;
	background-size: cover;
	border: 4px solid #fff;
	background-color: #ef5a93;
	opacity: 1;
}

.custom-icon-disable {
	cursor: pointer;
	display: flex;
	width: 40px;
	height: 40px;
	color: #fff;
	font-size: 20pt;
	border-radius: 50% 50%;
	overflow: hidden;
	background-size: cover;
	border: 4px solid #f4f7fa;
	background-color: #d3dce0;
	opacity: 1;
}

.custom-icon-disable {
	cursor: pointer;
	display: flex;
	width: 40px;
	height: 40px;
	color: #fff;
	font-size: 20pt;
	border-radius: 50%;
	overflow: hidden;
	background-size: cover;
	border: 4px solid #f4f7fa;
	background-color: #d3dce0;
	opacity: 1;
}

.padding-4 {
	padding-left: 20px !important;
	padding-bottom: 0px;
}

.bg-color-grey {
	background-color: #f4f7fa !important;
}

.theme--light.v-divider {
	border-color: rgb(130, 69, 90) !important;
}

.scrollbarvue {
	height: 560px !important;
}

.padding-left-title {
	padding-left: 0px !important;
}

@media only screen and (max-width: 1024px) {
	.scroll-area {
		position: relative;
		margin: auto;

		height: 450px;
	}

	.padding-left-title {
		padding-left: 15px !important;
	}

	@media only screen and (max-width: 750px) {
		.scroll-area {
			position: relative;
			margin: auto;

			height: 350px;
		}
	}
}

@media only screen and (max-width: 600px) {
	.padding-left-title {
		padding-left: 15px !important;
	}

	.padding-4 {
		padding-left: 0px !important;
	}

	.guest-flex-padding {
		padding: 0px 5px 10px 0px;
	}

	.cardLeft {
		padding-left: 0px;
	}

	.leftTitle {
		padding-left: 0px;
	}
}

@media only screen and (max-width: 1890px) {
	.cardsWH {
		width: 185px !important;
		height: 125px !important;
	}
}

@media only screen and (min-width: 600px) {
	.centerCards {
		margin-left: 20%;
	}
}

@media screen and (max-width: 960px) {
	.cardsWH {
		width: 190px !important;
		height: 130px !important;
	}

	.padding-4 {
		padding-left: 0px !important;
		padding-bottom: 0px;
	}
}

.filter-toolbars {
	background-color: white !important;
	border-bottom-style: solid !important;
	border-bottom-width: 1px !important;
	border-color: #cfd8dc;
}

.border-1px {
	border-style: solid;
	border-width: 1px;
	border-color: #cfd8dc;
}

.borderCards {
	border-style: solid;
	border-width: 1px;
	border-color: #cfd8dc;
}

.numberCards {
	font-family: Arial, Helvetica, sans-serif;
	font-size: 12pt;
	/*   font-weight:bold; */
	color: #494848;
}

.noselect {
	-webkit-touch-callout: none;
	/* iOS Safari */
	-webkit-user-select: none;
	/* Safari */
	-khtml-user-select: none;
	/* Konqueror HTML */
	-moz-user-select: none;
	/* Firefox */
	-ms-user-select: none;
	/* Internet Explorer/Edge */
	user-select: none;
	/* Non-prefixed version, currently
                                  supported by Chrome and Opera */
}

.cut-text {
	max-width: 10px;
	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;
}

.container .content-x {
	width: auto;
	height: 500px;
}

.fill-vertical {
  display: flex;
  flex-wrap: wrap;
  flex-direction: column;
  align-content: space-between;
}

.ps-container>.ps-scrollbar-x-rail,
.ps-container>.ps-scrollbar-y-rail {
	opacity: 0.6 !important;
}

.customBadge {
	width: 32px;
	height: 20px;
	padding-top: 1px;
	position: absolute;
	top: -5px;
	right: -5px;
	border-radius: 20px 20px 20px 20px;
	font-size: 12px;
	font-weight: 400;
}

.ps__rail-x {
	background-color: red !important;
}
</style>
