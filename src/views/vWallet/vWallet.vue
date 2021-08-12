<template>
<div>
	<v-container class="profile-empty-container">
		<v-card class=" desktop-image-profile" flat>
			<v-card-text class="no-padding">
				<v-tabs slot="extension" v-model="tabs" color="rgba(0,0,0,0)" grow :show-arrows="$vuetify.breakpoint.smAndDown">
					<v-tabs-slider color="primaryPurple"></v-tabs-slider>
					<v-tab>
						Resumen
					</v-tab>
					<v-tab>
						Enviar y Solicitar
					</v-tab>
					<v-tab>
						Cartera
					</v-tab>
					<v-tab>
						Movimientos
					</v-tab>
				</v-tabs>
			</v-card-text>
		</v-card>
	</v-container>
	<v-container class="no-padding">
		<v-tabs-items touchless v-model="tabs">
			<v-tab-item :transition="false" :reverse-transition="false">
				<v-sumary-wallet></v-sumary-wallet>
			</v-tab-item>
			<v-tab-item :transition="false" :reverse-transition="false">
			</v-tab-item>
			<v-tab-item :transition="false" :reverse-transition="false">
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
import SumaryWallet from "./sumaryWallet"

export default {
	components: {
		'v-sumary-wallet': SumaryWallet
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
			}
		};
	},
	computed: {
		swiper() {
			return this.$refs.mySwiper.swiper;
		},
		...mapGetters([
			'userProfile',
		]),
		userProfile: {
			get() {
				return JSON.parse(this.$store.getters.userProfile)
			}
		}

	},
	methods: {


	}
};
</script>
