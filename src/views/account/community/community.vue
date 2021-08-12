<template>
<div style="margin-top: -25px!important;">
	<v-container class="profile-empty-container no-padding">
		<v-layout>
			<v-flex xs12 lg12>
				<v-card class="desktop-image-profile" flat>
					<v-card-text class="no-padding">
						<v-tabs slot="extension" v-model="tabs" :slider-color="currentBussinesClub.theme.primary" color="rgba(0,0,0,0)" grow :show-arrows="$vuetify.breakpoint.smAndDown">
							<v-tab>Invitados</v-tab>
							<v-tab @click="reloadMap">Estadísticas</v-tab>
							<v-tab>Metas</v-tab>
						</v-tabs>
					</v-card-text>
				</v-card>
			</v-flex>
		</v-layout>
	</v-container>
	<v-container class="no-padding padding-top-24">
		<v-tabs-items touchless v-model="tabs">
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-community-resume :userProfile="userProfile"></v-community-resume>
			</v-tab-item >
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-community-statistics class="pt-3" :keyMap="locationMap"></v-community-statistics>
			</v-tab-item>
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-community-goals class="pt-3"></v-community-goals>
			</v-tab-item>
		</v-tabs-items>
	</v-container>
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
import CommunityResume from "./resume";
import CommunityStatistics from "./statistics";
import CommunityGoals from "./goals";

export default {
	components: {
		"v-community-resume": CommunityResume,
		"v-community-statistics": CommunityStatistics,
		"v-community-goals": CommunityGoals
	},
	data() {
		return {
			nameUser: "",
			email: "",
			tabs: 0,
			searchPanel: true,
			breadcrumbs: [{
					text: "Dashboard",
					disabled: false,
					href: "breadcrumbs_dashboard"
				},
				{
					text: "Link 1",
					disabled: false,
					href: "breadcrumbs_link_1"
				},
				{
					text: "Link 2",
					disabled: true,
					href: "breadcrumbs_link_2"
				}
			],

			drawer: true,
			slides: ["slide a", "slide b", "slide c"],
			items: [{
					title: "Home",
					icon: "dashboard"
				},
				{
					title: "About",
					icon: "question_answer"
				}
			],
			mini: true,
			right: true,
			swiperOption: {
				pagination: {
					el: ".swiper-pagination",
					//type:'bullets',
					clickable: true
				},
				autoplay: {
					delay: 3000,
					disableOnInteraction: false
				},
				loop: true
			},
			locationMap: 0,

		};
	},
	computed: {
		swiper() {
			return this.$refs.mySwiper.swiper;
		},
		...mapGetters(["userProfile", "currentBussinesClub"]),
		userProfile: {
			get() {
				return JSON.parse(this.$store.getters.userProfile);
			}
		}
	},
	methods: {
		reloadMap(){
			this.locationMap++;
		}
	}
};
</script>
