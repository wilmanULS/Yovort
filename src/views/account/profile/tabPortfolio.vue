<template>
<v-container class="no-padding">
	<v-layout row wrap v-if="!showImages">
		<v-flex xs12 md12>
			<v-card-text>
				<v-container grid-list-lg class="no-padding">
					<v-layout row wrap>
						<v-flex xs12 md3>
							<v-card height="300px" class="cardAdd borderCards">
								<v-container class="no-padding">
									<v-layout row wrap>
										<v-flex xs12 text-xs-center class="center">
											<div>
												<v-btn small :color="currentBussinesClub.theme.primary" fab dark @click="openPortfolioDialog(-1)">
													<v-icon>mdi mdi-plus</v-icon>
												</v-btn>
											</div>
											<span class="textAddSkill">{{$t('message.addSkillbook')}}</span>
										</v-flex>
									</v-layout>
								</v-container>
							</v-card>
						</v-flex>
						<v-flex xs12 md3 v-for="(item, index) in userProfile.briefcase" :key="index">
							<v-card class="borderCards"
							 :style="'height: 300px!important;' + (item.images.length>0?'background-image:url(\''+FMS_URL+'/image/fetch/'+item.images[0].coverImage+'?tx=resize(w:300,h:300)\')!important;background-size:cover!important;': '')">
								<div style="height:100%!important;width:100%!important;background-color:rgb(44, 53, 58,.7)!important;">
									<v-container class="no-padding">
										<v-flex xs12>
											<v-layout align-end justify-start column>
												<v-btn class="main-title" flat icon small @click="openPortfolioDialog(index)">
													<v-icon small color="white">mdi mdi-briefcase-edit</v-icon>
												</v-btn>
												<v-btn class="main-title" flat icon small @click="openDeleteDialog(index)">
													<v-icon small color="white">mdi mdi-briefcase-minus</v-icon>
												</v-btn>
											</v-layout>
										</v-flex>
										<v-layout>
											<v-flex xs12 text-xs-center class="pt-4">
												<div>
													<v-btn small outline fab color="white" @click="openBriefcase(index)">
														<v-icon small class="pb-1" color="white">mdi mdi-briefcase</v-icon>
													</v-btn>
												</div>
												<span class="textSkill">{{getObjectPath(item,'name','')}}</span>
											</v-flex>
										</v-layout>
									</v-container>
								</div>
							</v-card>
						</v-flex>
					</v-layout>
				</v-container>
			</v-card-text>
		</v-flex>
		<v-confirm-dialog v-bind:titleToolbar="$t('message.portfolioDelete')" ref="deletePortfolio" type="question" :heading="$t('message.portfolioDelete')"
		 v-bind:message="$t('message.portfolioDeleteAsk',{portfolio: getObjectPath(userProfile,'briefcase.'+indexPortfolio+'.name', '') })" @onConfirm="removePortfolio()"></v-confirm-dialog>
		<v-popup-dialog ref="portfolio" :model="portfolioDialog" :title="indexPortfolio<0?$t('message.addSkillbookTitle'):$t('message.editSkillbookTitle')" showRequired @onClose="closePortfolioDialog" @onSave="savePortfolio()">
			<v-text-field ref="namePortfolio" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.nameSkillbookLabel')+'*'" v-model="portfolioName" :rules="portfolioNameRules"></v-text-field>
		</v-popup-dialog>
	</v-layout>
	<v-layout row wrap v-else>
		<v-portfolio-images ref="portfolioImages" @onClosePortfolio="closePortfolio"></v-portfolio-images>
	</v-layout>
</v-container>
</template>
<script>
import confirmDialog from "Components/ySocialHome/confirmDialog";
import Vue from "vue";
import {
	mapGetters
} from "vuex";
import AppConfig from "Constants/AppConfig.js";
import PortfolioImages from "./tabPortfolioImages";
export default {
	props: {
		currentTab: Number
	},
	data() {
		return {
			FMS_URL: AppConfig.FMS_URL,
			portfolioName: "",
			getObjectPath: Vue.getObjectPath,
			portfolioDialog: false,
			indexPortfolio: -1,
			portfolioNameRules: [],
			showImages: false,
			portfolioId: null,
		};
	},
	computed: {
		...mapGetters(["userProfile", "currentBussinesClub"]),
		userProfile: {
			get() {
				if (this.$store.getters.userProfile != "")
					return JSON.parse(this.$store.getters.userProfile);
			}
		}
	},
	components: {
		"v-confirm-dialog": confirmDialog,
		"v-portfolio-images": PortfolioImages,
	},
	created() {
		this.portfolioNameRules = [v => !!v || this.$t('message.requiredField')];
	},
	methods: {
		/**
		 * Ask to delete the given portfolio
		 */
		openDeleteDialog(index) {
			this.indexPortfolio = index;
			this.$refs.deletePortfolio.openDialog();
		},

		/**
		 * Remove the given portfolio
		 */
		removePortfolio() {
			if (this.indexPortfolio < 0) {
				this.$refs.deletePortfolio.close();
				return;
			}

			let portfolio = {
				briefcase: [this.userProfile.briefcase[this.indexPortfolio]._id],
			};

			/* Call the portfolio delete action */
			this.$store.dispatch('deletePortfolio', portfolio).then(() => {
				this.$store.dispatch("doNotification", {
					msg: this.$t('message.infoUpdate'),
					type: 'success'
				});
			}).catch(() => {
				this.$store.dispatch("doNotification", {
					msg: this.$t("message.sessionErrorDefault"),
					type: 'error'
				});
			}).finally(() => {
				this.indexPortfolio = -1;
				this.$refs.deletePortfolio.close();
			});
		},

		/**
		 * Open the porfolio dialog
		 */
		openPortfolioDialog(index) {
			this.indexPortfolio = index;
			this.portfolioName = this.indexPortfolio >= 0 ? this.userProfile.briefcase[this.indexPortfolio].name : null;
			this.$refs.portfolio.doReset();
			this.portfolioDialog = true;
		},

		/**
		 * Close the porfolio dialog
		 */
		closePortfolioDialog() {
			this.portfolioDialog = false;
		},

		/**
		 * Save the portfolio information
		 */
		savePortfolio() {
			if (!this.$refs.portfolio.doValidate()) {
				return;
			}

			let action;
			let portfolio = {
				name: this.portfolioName,
			};

			/* Check if the portfolio is new or an update */
			if (this.indexPortfolio >= 0) {
				portfolio['portfolioId'] = this.userProfile.briefcase[this.indexPortfolio]._id;
				action = 'updatePortfolio';
			} else {
				action = 'addNewPortfolio';
			}

			/* Call the portfolio action */
			this.$store.dispatch(action, portfolio).then(() => {
				this.$store.dispatch("doNotification", {
					msg: this.$t('message.infoUpdate'),
					type: 'success'
				});
			}).catch(() => {
				this.$store.dispatch("doNotification", {
					msg: this.$t("message.sessionErrorDefault"),
					type: 'error'
				});
			}).finally(() => {
				this.indexPortfolio = -1;
				this.closePortfolioDialog();
			});
		},

		/**
		 * Open the detailed content of the given briefcase
		 */
		openBriefcase(index) {
			this.showImages = true;
			this.portfolioId = this.userProfile.briefcase[index]._id;
			Vue.nextTick(() => {
				this.$refs.portfolioImages.loadPortfolio(this.portfolioId);
			});
		},

		closePortfolio() {
			this.showImages = false;
			this.portfolioId = null;
		}
	}
};
</script>
<style>
.cardAdd {
	border: dashed 2px gray !important;
}

.textAddSkill {
	color: gray;
	font-size: 12px !important;
	text-transform: capitalize !important;
	font-weight: 700 !important;
}

.textSkill {
	font-size: 19px !important;
	text-transform: capitalize !important;
	font-weight: 700 !important;
}

.center {
	position: absolute;
	left: 50%;
	top: 50%;
	transform: translate(-50%, -50%);
	-webkit-transform: translate(-50%, -50%);
}
</style>
