<template>
<v-container v-if="mountedReady" grid-list-xs class="ma-0 pa-0">
	<v-card flat color="transparent" v-if="!openDetail">
		<v-card-actions>
			<v-spacer></v-spacer>
			<div>
				<v-tooltip left>
					<template slot="activator">
						<v-btn @click="openNewTicket()" :color="currentBussinesClub.theme.primary" icon>
							<v-icon color="white">help</v-icon>
						</v-btn>
					</template>
					<span>{{$t('message.requestAssistance')}}</span>
				</v-tooltip>
			</div>
		</v-card-actions>
	</v-card>
	<v-new-ticket ref="newTicket"></v-new-ticket>
	<v-card v-if="!openDetail" @click="selectTicket(ticket)" hover v-for="(ticket, index) in ticketsListUser" :key="index" class="mb-3">
		<v-card-text>
			<v-container class="pa-0" grid-list-{xs through xl}>
				<v-layout row wrap>
					<v-flex xs12>
						<span :style="'color:'+currentBussinesClub.theme.primary">{{ticket.topic}}</span>
					</v-flex>
					<v-flex xs12 class="grey--text" mb-2 style="font-size:12px;">{{ticket.createdAt | moment("dddd DD MMMM YYYY, h:mm:ss a")}}</v-flex>
					<v-flex xs11 style="margin-top:-8px;">
						<v-flex>
							<v-chip small outline color="#0a3568">{{$t('message.'+ticket.status)}}</v-chip>
						</v-flex>
					</v-flex>
					<v-flex xs1 style="position:absolute;top:35%;right:5%">
						<v-icon medium @click="selectTicket(ticket)">fa fa-angle-right</v-icon>
					</v-flex>
				</v-layout>
			</v-container>
		</v-card-text>
	</v-card>
	<v-fade-transition>
		<v-timeline-ticket ref="timeline" :profiles="profiles"></v-timeline-ticket>
	</v-fade-transition>
	<v-layout v-if="ticketsListUser.length==0" align-center justify-center row fill-height>{{$t('message.noTicketsHave')}}</v-layout>
</v-container>
</template>
<script>
import {
	mapGetters
} from "vuex";
import moment from "moment";
import timelineTicket from "./timelineTicket";
import newTicketDialog from "./newTicketDialog";
export default {
	components: {
		"v-timeline-ticket": timelineTicket,
		"v-new-ticket": newTicketDialog
	},
	computed: {
		...mapGetters([
			"userProfile",
			"currentBussinesClub",
			"ticketsListUser",
			"selectedTicketUser",
			"openDetail"
		])
	},
	data() {
		return {
			mountedReady: false,
			profiles: {}
		};
	},
	mounted() {
		this.$store.dispatch("getTicketsByUser", {
			parent: this.currentBussinesClub.clubId
		}).then(res => {
			this.mountedReady = true;
		});
	},
	methods: {
		selectTicket(ticket) {
			this.$root.$children[0].$emit(
				"showLoading",
				this.$t("message.loadingTicketInformation")
			);
			let payload = {
				users: [ticket.userId, ticket.agent]
			};
			this.$store.dispatch("getProfileUsers", payload).then(response => {
				this.profiles = response;
				this.$store.commit("setSelectedTicket", ticket);
				if (this.selectedTicketUser != null) {
					this.$refs.timeline.show();
				}
				this.$root.$children[0].$emit("hideLoading");
			});

		},
		openNewTicket() {
			this.$refs.newTicket.openDialog();
		}
	}
};
</script>
