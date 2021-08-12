<template>
<v-dialog v-model="open" max-width="450px" transition="dialog-bottom-transition">
	<v-card class="roundedCard">
		<v-img :src="selectedUserProfile.personal_info.banner||'/static/img/ciudad_banner_yovirt.png'" ref="banner" @error="imageLoadErrorBanner()" aspect-ratio="1.80" v-if="$vuetify.breakpoint.mdAndUp">
			<v-btn @click="$emit('onCancel')" flat icon absolute style="right:0%">
				<v-icon color="white">close</v-icon>
			</v-btn>
			<v-container fill-height class="no-padding mobile-darken-gradient" fluid>
				<v-speed-dial style="bottom:45px;" v-model="fab" bottom absolute direction="top" right id="x">
					<v-btn small slot="activator" v-model="fab" :color="currentBussinesClub.theme.primary" dark fab @click="toggleProfileTooltip">
						<v-icon>fas fa-list</v-icon>
						<v-icon>close</v-icon>
					</v-btn>
					<v-tooltip left z-index="999" relative>
						<v-btn id="userbtn" fab dark small :color="currentBussinesClub.theme.primary" slot="activator">
							<v-icon>fas fa-comments</v-icon>
						</v-btn>
						{{$t('message.message')}}
					</v-tooltip>
					<v-tooltip left z-index="999" relative>
						<v-btn fab dark small id="camerabtn" :color="currentBussinesClub.theme.secondary" slot="activator">
							<v-icon>fas fa-user-plus</v-icon>
						</v-btn>
						{{$t('message.addToMyFriends')}}
					</v-tooltip>
					<v-tooltip left z-index="999" relative>
						<v-btn id="userbtn" fab dark small :color="currentBussinesClub.theme.primary" slot="activator">
							<v-icon>fas fa-user-lock</v-icon>
						</v-btn>
						{{$t('message.blockUser')}}
					</v-tooltip>
				</v-speed-dial>
			</v-container>
		</v-img>
		<v-img :src="selectedUserProfile.personal_info.banner||'/static/img/ciudad_banner_yovirt.png'" ref="banner" @error="imageLoadErrorBanner()" aspect-ratio="1.80" v-if="$vuetify.breakpoint.smAndDown">
			<v-speed-dial style="bottom:13%;" v-model="fab" bottom absolute direction="top" right small>
				<v-btn small slot="activator" v-model="fab" :color="currentBussinesClub.theme.primary" dark fab @click="toggleProfileTooltip">
					<v-icon>fas fa-list</v-icon>
					<v-icon>close</v-icon>
				</v-btn>

				<v-tooltip left z-index="999" relative>
					<v-btn id="userbtn" fab dark small :color="currentBussinesClub.theme.primary" slot="activator">
						<v-icon>fas fa-comments</v-icon>
					</v-btn>
					{{$t('message.message')}}
				</v-tooltip>
				<v-tooltip left z-index="999" relative>
					<v-btn fab dark small id="camerabtn" :color="currentBussinesClub.theme.secondary" slot="activator">
						<v-icon>fas fa-user-plus</v-icon>
					</v-btn>
					{{$t('message.addToMyFriends')}}
				</v-tooltip>
				<v-tooltip left z-index="999" relative>
					<v-btn id="userbtn" fab dark small :color="currentBussinesClub.theme.primary" slot="activator">
						<v-icon>fas fa-user-lock</v-icon>
					</v-btn>
					{{$t('message.blockUser')}}
				</v-tooltip>
			</v-speed-dial>
			<v-container fill-height fluid class="mobile-darken-gradient">
				<v-layout align-center justify-center column fill-height>
					<v-avatar :size="96">
						<v-img
						 :src="`${FMS_URL}/image/fetch/${getObjectPath(selectedUserProfile, 'personal_info.picture','/static/img/perfil_yovirt.png')}?tx=resize(w:96,h:96)&username=${getObjectPath(selectedUserProfile, 'personal_info.names','')} ${getObjectPath(selectedUserProfile, 'personal_info.surnames','')}&color=${getObjectPath(selectedUserProfile, 'personal_info.color','no')}&size=128`"
						 lazy-src="/static/img/perfil_yovirt_small.png" ref="imgProfile" class="user-profile-img"></v-img>
					</v-avatar>
				</v-layout>
			</v-container>
		</v-img>
		<v-layout style="max-height:50px" row v-if="$vuetify.breakpoint.mdAndUp">
			<v-flex xs12>
				<v-avatar size="128" class="profileInformation">
					<v-img ref="imgProfile" class="user-avatar-desktop user-profile-img"
					 :src="`${FMS_URL}/image/fetch/${getObjectPath(selectedUserProfile, 'personal_info.picture','/static/img/perfil_yovirt.png')}?tx=resize(w:96,h:96)&username=${getObjectPath(selectedUserProfile, 'personal_info.names','')} ${getObjectPath(selectedUserProfile, 'personal_info.surnames','')}&color=${getObjectPath(selectedUserProfile, 'personal_info.color','no')}&size=128`"
					 lazy-src="/static/img/perfil_yovirt_small.png"></v-img>
				</v-avatar>
			</v-flex>
		</v-layout>
		<v-container grid-list-xs pb-1>
			<v-card color="transparent" flat>
				<v-layout row wrap>
					<v-flex xs3 text-xs-center>
						<span class="styleNumbers">0</span>
						<span class="subTitleCard">{{$t('message.friends')}}</span>
					</v-flex>
					<v-flex xs3 text-xs-center>
						<span class="styleNumbers">0</span>
						<span class="subTitleCard">N1 {{$t('message.actives')}}</span>
					</v-flex>
					<v-flex xs3 text-xs-center>
						<span class="styleNumbers">{{ getObjectPath(selectedUserProfile, 'referredCountN1','0')}}</span>
						<span class="subTitleCard">{{$t('message.inactives')}}</span>
					</v-flex>
					<v-flex xs3 text-xs-center>
						<span class="styleNumbers">{{getObjectPath(selectedUserProfile, 'referredCount','0')}}</span>
						<span class="subTitleCard">N+ {{$t('message.total')}}</span>
					</v-flex>
				</v-layout>
			</v-card>
			<v-card class="text-xs-center ma-0 pa-0" flat>
				<v-flex xs12 class="pb-2" style="margin-top: 1rem !important;">
					<v-flex xs12 v-if="selectedUserProfile.personal_info.names">
						<span class="titleName">{{ getObjectPath(selectedUserProfile, 'personal_info.names','')}}&nbsp;{{ getObjectPath(selectedUserProfile, 'personal_info.surnames','')}}</span>
					</v-flex>
					<v-flex xs12 v-if="selectedUserProfile.personal_info.occupation">
						<span class="subTitleName">{{ getObjectPath(selectedUserProfile, 'personal_info.occupation','')}}</span>
					</v-flex>
					<v-flex xs12 v-if="selectedUserProfile.personal_info.addresses">
						<span class="subTitleName">
							<span v-if="getObjectPath(selectedUserProfile, 'personal_info.addresses.address.city',null)">{{ getObjectPath(selectedUserProfile, 'personal_info.addresses.address.city','')}},&nbsp;</span>
							<span v-if="getObjectPath(selectedUserProfile, 'personal_info.addresses.address.country',null)">{{ getCountry(getObjectPath(selectedUserProfile, 'personal_info.addresses.address.country','')) }}</span>
						</span>
					</v-flex>
				</v-flex>
			</v-card>
		</v-container>
	</v-card>
</v-dialog>
</template>
<script>
import {
	mapGetters
} from "vuex";
import Vue from "vue";
import AppConfig from 'Constants/AppConfig'
export default {
	props: ["heading", "message", "method", "titleToolbar"],
	data() {
		return {
			getObjectPath: Vue.getObjectPath,
			fab: false,
			profileTooltip: false,
			open: false,
			FMS_URL: AppConfig.FMS_URL
		};
	},
	computed: {
		...mapGetters(["selectedUserProfile", "currentBussinesClub", "getCountry"]),
	},
	methods: {
		imageLoadErrorBanner() {
			this.$refs.banner.src = "/static/img/ciudad_banner_yovirt.gif";
		},
		imageLoadErrorProfile() {
			this.$refs.imgProfile.src = "/static/img/perfil_yovirt.png";
		},
		toggleProfileTooltip() {
			setTimeout(() => {
				if (this.fab) {
					if (this.profileTooltip) {
						this.profileTooltip = false;
					} else {
						this.profileTooltip = true;
					}
				} else {
					this.profileTooltip = false;
				}
			}, 200);
		},
		openDialog() {
			this.open = true;
		},
		close() {
			this.open = false;
		},
		calculate_age(dob) {
			let birthdate = new Date(dob);
			var diff_ms = Date.now() - birthdate.getTime();
			var age_dt = new Date(diff_ms);
			return Math.abs(age_dt.getUTCFullYear() - 1970);
		}
	}
};
</script>
<style>
.confirmDialog {
	font-size: 14px;
	color: black;
	font-weight: 500;
	padding-bottom: 20px;
	padding-left: 5%;
}

.profileInformation {
	display: block !important;
	position: relative !important;
	border-radius: 50% !important;
	width: 130px !important;
	height: 130px !important;
	top: -60%;
	right: -36%;
	z-index: 0 !important;
}

.profile img {
	border-radius: 50%;
	width: 130px;
	height: 130px;
	max-width: 100%;
	z-index: 0;
}

.subTitleCard {
	font-size: 13px;
	color: #9ca9b8;
}

.titleName {
	color: #53607f;
	font-size: 20px;
}

.subTitleName {
	font-size: 12px;
	font-weight: 570;
	color: #53607f;
}

.styleNumbers {
	font-size: 16px;
	font-weight: bold !important;
	color: #53607f;
}

.roundedCard {
	border-radius: 10px 10px 10px 10px;
}
</style>
