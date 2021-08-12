<template>
<div v-bind:style="{'background-image': `linear-gradient(to bottom right, ${currentBussinesClub.theme.secondary}, ${currentBussinesClub.theme.primary})`}" lazy>
	<v-container fill-height fluid class="loginContainerLayer yovirt-layer">
		<v-layout row fill-height>
			<div style="position: absolute; top: 10px; right: 20px; z-index:1000;">
				<v-lang-provider></v-lang-provider>
			</div>
			<v-flex v-if="$vuetify.breakpoint.mdAndUp" class="flex-side-picture">
				<div class="sidePicture text-xs-center" v-bind:style="{'background-image':'url(\'/static/img/yovirt-banner-low.png\')'}"></div>
			</v-flex>
			<v-flex style="min-height: 100vh!important;" v-bind:class="{'flex-form-full-column':$vuetify.breakpoint.smAndDown, 'flex-form-column':$vuetify.breakpoint.mdAndUp }">
				<v-container>
					<v-layout align-center justify-center column fill-height>
						<v-flex></v-flex>
						<v-flex v-bind:class="{'md-limit-sm':$vuetify.breakpoint.smAndDown, 'md-limit-md':$vuetify.breakpoint.mdAndUp }">
							<v-card color="transparent" flat>
								<v-img :src="`${currentBussinesClub.settings.logo_url_large}`" aspect-ratio="3" contain></v-img>
								<v-card-title primary-title>
									<div>
										<h3 class="headline mb-2 white--text">{{$t('message.thanksRejectJoin')}}</h3>
										<div class="mb-3 white--text">{{$t('message.thanksRejectJoinDescription')}}</div>
										<v-container fluid class="tabs-items-container">
											<v-btn @click="redirectToClub" class="rounded-login-buttons rounded-login-buttons-text">{{$t('message.sessionLogIn')}}</v-btn>
										</v-container>
									</div>
								</v-card-title>
							</v-card>
						</v-flex>
						<v-flex></v-flex>
					</v-layout>
				</v-container>
			</v-flex>
		</v-layout>
	</v-container>
</div>
</template>

<script>
import {
	mapGetters
} from "vuex";
import Vue from "vue";
import LangProvider from "../../layout/header/langProvider";
import AppConfig from "../../constants/AppConfig.js";
export default {
	data() {
		return {
			FMS_URL: AppConfig.FMS_URL
		};
	},
	components: {
		"v-lang-provider": LangProvider
	},
	computed: {
		...mapGetters(["currentBussinesClub"])
	},
	mounted() {
		let url = new URL(window.location.href);
		let referral = url.searchParams.get("reject");
		if (referral) {
			this.$store.dispatch("rejectMemberShip", referral).then(res => {
				var errorKey = res.data.data.error;
				switch (errorKey) {
					case 0:
						this.$store.dispatch("doNotification", {
							msg: this.$t("message.membershipReject"),
							type: "success"
						});
						break;
					case 1:
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
			});
		}
	},
	created() {},
	methods: {
		redirectToClub() {
			window.location =
				"https://" + AppConfig.SUBDOMAIN_URL + "." + AppConfig.BASE_URL;
		}
	}
};
</script>

<style>
</style>
