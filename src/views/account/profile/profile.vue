<template>
<div style="margin-top: -25px!important;">
	<v-container class="profile-empty-container no-padding">
		<v-layout>
			<v-flex xs12 lg12>
				<v-card class="desktop-image-profile" flat>
					<v-card-text class="no-padding">
						<v-tabs slot="extension" v-model="tabsButtons " grow :show-arrows="$vuetify.breakpoint.smAndDown">
							<v-tabs-slider :color="currentBussinesClub.theme.primary"></v-tabs-slider>
							<v-tab @click="slide(0)">
								{{$t('message.tabPersonalInfo')}}
							</v-tab>
							<v-tab @click="slide(1)">
								{{$t('message.tabSocialNetwork')}}
							</v-tab>
							<v-tab @click="slide(2)">
								{{$t('message.tabEducation')}}
							</v-tab>
							<v-tab @click="slide(3)">
								{{$t('message.tabJobs')}}
							</v-tab>
							<v-tab @click="slide(4)">
								{{$t('message.tabHobbies')}}
							</v-tab>
							<v-tab @click="slide(5)">
								{{$t('message.tabPortfolio')}}
							</v-tab>
							<v-tab @click="slide(6)">
								{{$t('message.tabAccountSettings')}}
							</v-tab>
						</v-tabs>
					</v-card-text>
				</v-card>
			</v-flex>
		</v-layout>
	</v-container>
	<v-container class="no-padding padding-top-24" v-if="userProfile != ''">
		<v-tabs-items touchless v-model="tabs">
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-personal-info :currentTab="tabs"></v-personal-info>
			</v-tab-item>
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-social-networks :currentTab="tabs"></v-social-networks>
			</v-tab-item>
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-education :currentTab="tabs"></v-education>
			</v-tab-item>
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-jobs :currentTab="tabs"></v-jobs>
			</v-tab-item>
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-hobbies :currentTab="tabs"></v-hobbies>
			</v-tab-item>
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-portfolio :currentTab="tabs"></v-portfolio>
			</v-tab-item>
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-account-settings :currentTab="tabs"></v-account-settings>
			</v-tab-item>
		</v-tabs-items>
	</v-container>
	<v-confirm-dialog v-bind:titleToolbar="$t('message.changesWithoutSaving')" ref="changesDialog" type="question" :heading="$t('message.changesWithoutSaving')" @onCancel="cancelDialogChanges" v-bind:message="$t('message.askSave')"
	 @onConfirm="saveDialogChanges"></v-confirm-dialog>
</div>
</template>
<script>
import Vue from "vue";
import {
	getCurrentAppLayout
} from "Helpers/helpers";
import {
	mapGetters
} from "vuex";
import TabPersonal from "./tabPersonal";
import TabSocialNetworks from "./tabSocialNetworks";
import TabEducation from "./tabEducation";
import TabJobs from "./tabJobs";
import TabHobbies from "./tabHobbies";
import TabSettings from "./tabSettings";
import TabPortfolio from "./tabPortfolio";
import {
	EventBus
} from "../../../eventBus.js"
import confirmDialog from "../../../components/ySocialHome/confirmDialog";
export default {
	components: {
		'v-personal-info': TabPersonal,
		'v-social-networks': TabSocialNetworks,
		'v-education': TabEducation,
		'v-jobs': TabJobs,
		'v-hobbies': TabHobbies,
		'v-account-settings': TabSettings,
		"v-confirm-dialog": confirmDialog,
		'v-portfolio': TabPortfolio
	},
	mounted() {
		EventBus.$on('canceledChanges', data => {
			this.tabs = nextTab;
		})
		EventBus.$on('savedChanges', data => {
			this.tabs = nextTab;
		})
		EventBus.$on('canNotSave', data => {
			if (data.block) {
				this.canSave = false;
				this.currentView = data.currentView;
			}
		})

	},
	data() {
		return {
			currentView: '',
			auxTab: 0,
			canSave: true,
			nameUser: "",
			email: "",
			tabs: 0,
			tabsButtons: 0,
			nextTab: 0,
			searchPanel: true,
			drawer: true,
			mini: true,
			right: true,
			swiperOption: {
				pagination: {
					el: ".swiper-pagination",
					clickable: true
				},
				autoplay: {
					delay: 3000,
					disableOnInteraction: false
				},
				loop: true
			}
		};
	},
	computed: {
		swiper() {
			return this.$refs.mySwiper.swiper;
		},
		...mapGetters([
			'userProfile',
			"currentBussinesClub",
			"profileChanges",
			"jobGroups"
		]),
		userProfile: {
			get() {
				if (this.$store.getters.userProfile != '')
					return JSON.parse(this.$store.getters.userProfile)
			}
		}

	},
	methods: {
		slide(val) {
			this.nextTab = val;
			if (this.profileChanges.hasChanges) {
				switch (this.profileChanges.view) {
					case 'personal_info':
						this.tabsButtons = this.tabs;
						this.$refs.changesDialog.openDialog()
						break;
					case 'social_networks':
						this.tabsButtons = this.tabs;
						this.$refs.changesDialog.openDialog()
						break;
				}
			} else {
				this.tabs = val;
			}

		},

		saveDialogChanges() {
			switch (this.profileChanges.view) {
				case 'personal_info':
					EventBus.$emit('personal_info:saveChanges');
					this.$refs.changesDialog.close()
					break;
				case 'social_networks':
					EventBus.$emit('social_networks:saveChanges');
					this.$refs.changesDialog.close()
					break;
			}
		},
		cancelDialogChanges() {
			switch (this.profileChanges.view) {
				case 'personal_info':
					EventBus.$emit('personal_info:cancelChanges');
					//this.$refs.changesDialog.close()
					break;
				case 'social_networks':
					EventBus.$emit('social_networks:cancelChanges');
					this.$refs.changesDialog.close()
					break;
			}
			/*If form is valid then change to selected tab*/
			if (this.canSave) {
				this.tabs = this.nextTab;
				this.tabsButtons = this.tabs;
			} else {
				/* If form is invalid then stay on current tab*/
				switch (this.currentView) {
					case 'personal_info':
						this.tabs = 0;
						break;
				}
				this.tabsButtons = this.tabs;
				this.canSave = true;
			}

		},
		redirectSettings(path) {
			this.$router.push(path)
		}
	}
};
</script>
