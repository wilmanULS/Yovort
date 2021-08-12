<template>
<v-dialog v-model="open" flat persistent transition="dialog-bottom-transition" fullscreen hide-overlay scrollable>
	<v-card tile>
		<v-toolbar class="linear-gradient" dense card dark>
			<v-toolbar-title>{{$t('message.businessClubRulesTitle')}}</v-toolbar-title>
		</v-toolbar>
		<v-card-text>
			<v-layout align-center justify-center column fill-height style="align-self: flex-end;">
				<vue-perfect-scrollbar ref="scrollbar" class="scroll-area">
					<v-flex style="text-align:justify;" pa-5>
						<span v-html="descriptionClub"></span>
					</v-flex>
					<v-flex style="text-align:justify;" pa-5>
						<ol v-if="rulesClub.length>0">
							<li class="custom-li" v-for="(rule, i) in rulesClub" :key="i">
								<span v-html="rule.descriptionRule"></span>
							</li>
						</ol>
					</v-flex>
					<v-flex>
						<v-layout align-center justify-center row fill-height>
							<v-flex xs3 class="pa-2">
								<v-btn :color="currentBussinesClub.theme.primary" dark round @click="closeDialog()">Aceptar</v-btn>
							</v-flex>
						</v-layout>
					</v-flex>
				</vue-perfect-scrollbar>
			</v-layout>
		</v-card-text>
	</v-card>
</v-dialog>
</template>
<script>
import {
	mapGetters
} from "vuex";
export default {
	computed: {
		...mapGetters(["currentBussinesClub", "rulesClub", "descriptionClub"])
	},
	data() {
		return {
			open: false,
			mountedReady: false
		};
	},
	mounted() {
		this.$store
			.dispatch("getDescriptionClub", this.currentBussinesClub.clubId)
			.then(res => {
				this.$store
					.dispatch("getRules", this.currentBussinesClub.clubId)
					.then(res => {
						this.mountedReady = true;
					});
			});
	},
	methods: {
		openDialog() {
			this.open = true;
		},
		closeDialog() {
			this.open = false;
		}
	}
};
</script>
