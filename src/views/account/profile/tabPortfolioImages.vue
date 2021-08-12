<template>
<v-container class="no-padding">
	<v-layout row wrap>
		<v-flex xs12 md12>
			<v-card-text>
				<v-container grid-list-lg class="no-padding">
					<v-layout row wrap>
						<v-flex xs12>
							<h3>
								<v-btn small flat dark icon @click="$emit('onClosePortfolio')">
									<v-icon style="color:black!important;">mdi mdi-chevron-left</v-icon>
								</v-btn>&nbsp;{{portfolio.name}}
							</h3>
						</v-flex>
					</v-layout>
					<v-layout row wrap>
						<v-flex xs12 md3>
							<v-card height="300px" :class="'borderCards'+(dragging?' dropAreaDragging':' cardAdd')">
								<v-container class="no-padding" fill-height>
									<v-layout row wrap fill-height>
										<v-flex xs12 fill-height>
											<form role="form" enctype="multipart/form-data" @submit.prevent="onSubmit" fill-height>
												<div class="drop-area" @drag="ondrag" @dragstart="ondragstart" @dragend="ondragend" @dragover="ondragover" @dragenter="ondragenter" @dragleave="ondragleave" @drop="ondrop">
													<v-container class="no-padding">
														<v-layout row wrap>
															<v-flex xs12 text-xs-center class="center" @dragenter="ondragenter" @dragleave="ondragleave">
																<div>
																	<input type="file" ref="files" name="files" @change="ondrop" multiple hidden>
																	<v-btn small :color="currentBussinesClub.theme.primary" fab dark @click="$refs.files.click()">
																		<v-icon>mdi mdi-upload</v-icon>
																	</v-btn>
																</div>
																<span class="textAddImage">{{$t('message.addBriefcaseImages')}}</span>
															</v-flex>
														</v-layout>
													</v-container>
												</div>
											</form>
										</v-flex>
									</v-layout>
								</v-container>
							</v-card>
						</v-flex>
						<v-flex xs12 md3 v-for="(item, index) in portfolio.images" :key="index">
							<v-portfolio-image-item :portfolioId="portfolioId" :item="item" :index="index"></v-portfolio-image-item>
						</v-flex>
					</v-layout>
				</v-container>
			</v-card-text>
		</v-flex>
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
import FileManagerService from "Services/FileManagerService.js";
import PortfolioImageItem from "./tabPortfolioImageItem";

export default {
	watch: {
		userProfile(value) {
			this.loadPortfolio(this.portfolioId);
		},
	},
	data() {
		return {
			FMS_URL: AppConfig.FMS_URL,
			getObjectPath: Vue.getObjectPath,
			portfolioDialog: false,
			indexPortfolio: -1,
			portfolioNameRules: [],
			portfolio: {},
			dragging: false,
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
		"v-portfolio-image-item": PortfolioImageItem,
	},
	created() {
		this.portfolioNameRules = [v => !!v || this.required];
	},
	methods: {
		ondrag(event) {
			event.preventDefault();
			event.stopPropagation();
		},
		ondragstart(event) {
			event.preventDefault();
			event.stopPropagation();
		},
		ondragend(event) {
			event.preventDefault();
			event.stopPropagation();
		},
		ondragover(event) {
			event.preventDefault();
			event.stopPropagation();
		},
		ondragenter(event) {
			event.preventDefault();
			event.stopPropagation();
			this.dragging = true;
		},
		ondragleave(event) {
			event.preventDefault();
			event.stopPropagation();
			this.dragging = false;
		},
		ondrop(event) {
			event.preventDefault();
			event.stopPropagation();

			/* Get the selected files */
			let files = event.target.files || event.dataTransfer.files;

			/* Initialize the form data */
			let formData = new FormData();

			/* Iteate all files to check images */
			let images = [];
			let cnt = 0;
			for (let i = 0; i < files.length; i++) {
				/* Check if the file is a valid image by extension */
				let file = files[i];
				if (/\.(jpe?g|png|gif|bmp)$/i.test(file.name) && cnt < 20) {
					cnt++;
					formData.append('files', file, file.name);
				}
			}

			/* Upload the images to the file manager service */
			FileManagerService.uploadFiles(this.portfolio._id, formData)
				.then((res) => {
					/* Get the images information */
					const images = res.data.data.files.map(file => {
						return {
							name: '',
							description: '',
							link: '',
							coverImage: `${this.portfolio._id}/${file.path}`,
						};
					});

					/* Upload the images information to the portfolio */
					this.$store.dispatch("addPortfolioImages", {
						portfolioId: this.portfolio._id,
						images: images
					}).then(() => {
						this.$store.dispatch("doNotification", {
							msg: this.$t('message.infoUpdate'),
							type: 'success'
						});
					}).catch(() => {
						this.$store.dispatch("doNotification", {
							msg: this.$t("message.sessionErrorDefault"),
							type: 'error'
						});
					});
				}).catch(() => {
					this.$store.dispatch("doNotification", {
						msg: this.$t("message.sessionErrorDefault"),
						type: 'error'
					});
				})
		},

		/**
		 * Load the portfolio information from user profile
		 */
		loadPortfolio(portfolioId) {
			if (portfolioId !== null) {
				this.portfolioId = portfolioId;
				const match = this.userProfile.briefcase.filter(briefcase => briefcase._id === portfolioId);
				this.portfolio = match.length === 1 ? match[0] : null;
			} else {

			}

		},

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
	}
};
</script>
<style scoped>
.cardAdd {
	border: dashed 2px gray !important;
}

.textAddImage {
	color: gray;
	font-size: 12px !important;
	font-weight: 700 !important;
}

.center {
	position: absolute;
	left: 50%;
	top: 50%;
	transform: translate(-50%, -50%);
	-webkit-transform: translate(-50%, -50%);
}

.drop-area {
	width: 100% !important;
	height: 100% !important;
}

.dropAreaDragging {
	border: dashed 2px var(--primary) !important;
}
</style>
