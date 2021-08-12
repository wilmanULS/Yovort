<template>
<v-dialog v-model="open" max-width="450px" transition="dialog-bottom-transition" lazy>
	<v-card>
		<v-img :src="userProfile.personal_info.banner" lazy-src="/static/img/ciudad_banner_yovirt_small.png" aspect-ratio="1.80" v-if="$vuetify.breakpoint.mdAndUp">
			<v-btn @click="close()" flat icon absolute style="right:0%">
				<v-icon color="white">close</v-icon>
			</v-btn>

			<v-container fill-height class="no-padding mobile-darken-gradient" fluid>
				<v-speed-dial style="bottom:45px;" v-model="fab" bottom absolute direction="top" right id="x" :color="currentBussinesClub.theme.accent">
					<v-btn small slot="activator" v-model="fab" color="accent" dark fab @click="toggleProfileTooltip">
						<v-icon>fas fa-list</v-icon>
						<v-icon>close</v-icon>
					</v-btn>
					<v-tooltip left v-model="profileTooltip" allow-overflow>
						<v-btn id="userbtn" fab dark small :color="currentBussinesClub.theme.primary" slot="activator" @click="">
							<v-icon>fas fa-user-plus</v-icon>
						</v-btn>
						{{$t('message.addMember')}}
					</v-tooltip>
					<v-tooltip left v-model="profileTooltip">
						<v-btn fab dark small id="camerabtn" :color="currentBussinesClub.theme.secondary" slot="activator" @click="">
							<v-icon>fas fa-user-slash</v-icon>
						</v-btn>
						{{$t('message.rejectMember')}}
					</v-tooltip>
					<v-tooltip left v-model="profileTooltip" allow-overflow>
						<v-btn id="userbtn" fab dark small :color="currentBussinesClub.theme.primary" slot="activator" @click="">
							<v-icon>fas fa-portrait</v-icon>
						</v-btn>
						{{$t('message.viewPortfolio')}}
					</v-tooltip>
				</v-speed-dial>
			</v-container>
		</v-img>

		<v-img :src="dataMember.photoUrl" aspect-ratio="1.80" v-if="$vuetify.breakpoint.smAndDown">
			<v-speed-dial style="bottom:13%;" v-model="fab" bottom absolute direction="top" right small @blur="">
				<v-btn small slot="activator" v-model="fab" color="accent" dark fab @click="toggleProfileTooltip">
					<v-icon>fas fa-list</v-icon>
					<v-icon>close</v-icon>
				</v-btn>
				<v-tooltip left v-model="profileTooltip" allow-overflow>
					<v-btn id="userbtn" fab dark small :color="currentBussinesClub.theme.primary" slot="activator" @click="">
						<v-icon>fas fa-user-plus</v-icon>
					</v-btn>

					{{$t('message.addMember')}}
				</v-tooltip>
				<v-tooltip left v-model="profileTooltip">
					<v-btn fab dark small id="camerabtn" :color="currentBussinesClub.theme.secondary" slot="activator" @click="">
						<v-icon>fas fa-user-slash</v-icon>
					</v-btn>
					{{$t('message.rejectMember')}}
				</v-tooltip>
				<v-tooltip left v-model="profileTooltip" allow-overflow>
					<v-btn id="userbtn" fab dark small :color="currentBussinesClub.theme.primary" slot="activator" @click="">
						<v-icon>fas fa-portrait</v-icon>
					</v-btn>
					{{$t('message.viewPortfolio')}}
				</v-tooltip>
			</v-speed-dial>
			<v-container fill-height fluid class="mobile-darken-gradient">
				<v-layout align-center justify-center column fill-height>
					<v-avatar :size="85">
						<v-img :src="dataMember.photoUrl" alt="avatar" class="user-profile-img"></v-img>
					</v-avatar>

				</v-layout>
			</v-container>
		</v-img>
		<v-layout row v-if="$vuetify.breakpoint.mdAndUp">
			<v-flex xs12>
				<v-avatar size="120" class="profileInformation">
					<v-img class="user-avatar-desktop user-profile-img" :src="dataMember.photoUrl">
					</v-img>
				</v-avatar>
			</v-flex>

		</v-layout>

		<v-container grid-list-xs>
			<v-card class="text-xs-center" flat color="transparent">
				<v-flex xs12 class="pb-2">
					<span class="titleName">{{dataMember.firstName}} {{dataMember.lastName}}</span><br>
					<span class="subTitleName">{{dataMember.email}} </span><br>
					<span class="subTitleName">{{dataMember.vCode}}</span>
				</v-flex>
				<v-divider :color="currentBussinesClub.theme.primary"></v-divider>
				<v-flex xs12>
					<span v-if="dataMember.status_flag_catalog == 0">Pendiente <v-icon color="primaryPink">fas fa-user-clock small</v-icon></span>
					<span v-if="dataMember.status_flag_catalog == 2">Aprobado <v-icon color="primaryPurple">fas fa-user-check small</v-icon></span>
					<span v-if="dataMember.status_flag_catalog == 4">Rechazado <v-icon color="pink">fas fa-user-times small</v-icon></span>
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
export default {
	props: ["heading", "message", "method", "titleToolbar", 'dataMember'],
	data() {
		return {

			fab: false,
			profileTooltip: false,
			open: false
		};
	},
	computed: {
		...mapGetters([
			'userProfile',
			"currentBussinesClub",
		]),
		userProfile: {
			get() {
				return JSON.parse(this.$store.getters.userProfile)
			}
		}
	},
	methods: {

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
	margin: -90px 0 15px 150px !important;
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
	font-size: 12px;
	color: gray;
}

.titleName {
	font-size: 22px;
	font-weight: 570;
}

.subTitleName {
	font-size: 11px;
	font-weight: 570;
}

.styleNumbers {
	font-weight: bold !important;
}
</style>
