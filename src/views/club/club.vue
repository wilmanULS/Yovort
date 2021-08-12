<template>
    <div style="margin-top: -25px!important;">
        <v-container class="profile-empty-container  no-padding">
            <v-layout>
    			<v-flex xs12 lg12>
    				<v-card class="desktop-image-profile" flat>
    					<v-card-text class="no-padding">
                            <v-tabs slot="extension" v-model="tabs" grow :show-arrows="$vuetify.breakpoint.smAndDown">
                                <v-tabs-slider :color="currentBussinesClub.theme.primary"></v-tabs-slider>
                                <v-tab>{{$t('message.myClubs')}}</v-tab>
                                <v-tab v-if="getBusinessClubRole">{{$t('message.members')}}</v-tab>
                                <v-tab >{{$t('message.clubRules')}}</v-tab>
                                <v-tab v-if="getBusinessClubRole">{{$t('message.ticketAdministration')}}</v-tab>
                                <v-tab v-if="getBusinessClubRole">{{$t('message.faqAdministration')}}</v-tab>
								<v-tab v-if="getBusinessClubRole">{{$t('message.clubSettings')}}</v-tab>
                            </v-tabs>
    					</v-card-text>
    				</v-card>
    			</v-flex>
    		</v-layout>
        </v-container>
        <v-container class="no-padding padding-top-24">
            <v-tabs-items touchless v-model="tabs">
                <v-tab-item :transition="false" :reverse-transition="false"><v-my-clubs></v-my-clubs></v-tab-item>
                <v-tab-item :transition="false" :reverse-transition="false" v-if="getBusinessClubRole"><v-club-members></v-club-members></v-tab-item>
                <v-tab-item :transition="false" :reverse-transition="false"><v-club-rules></v-club-rules></v-tab-item>
                <v-tab-item :transition="false" :reverse-transition="false" v-if="getBusinessClubRole"><v-tickets></v-tickets></v-tab-item>
                <v-tab-item lazy :transition="false" :reverse-transition="false" v-if="getBusinessClubRole"><v-faqs></v-faqs></v-tab-item>
                <v-tab-item :transition="false" :reverse-transition="false" v-if="getBusinessClubRole"><v-club-settings></v-club-settings></v-tab-item>
            </v-tabs-items>
        </v-container>
    </div>
  </template>
<script>
import {
	mapGetters
} from "vuex";
import MyClubs from "./myClubs";
import ClubMembersActive from "./membersActive";
import ClubRules from "./rules";
import ClubSettings from "./settings";
import Tickets from "./support/tickets/tickets"
import Faqs from "./support/faqs/faqs"
import {
	watch
} from 'fs';
export default {
	computed: {
		...mapGetters([
			'currentBussinesClub',
			'getBusinessClubRole'
		]),
	},
	components: {
		'v-my-clubs': MyClubs,
		'v-club-members': ClubMembersActive,
		'v-club-rules': ClubRules,
		'v-club-settings': ClubSettings,
		'v-tickets': Tickets,
		'v-faqs': Faqs
	},
	data() {
		return {
			tabs: 0,
			search: '',
		}
	},
}
</script>
