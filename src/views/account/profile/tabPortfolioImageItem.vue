<template>
<v-card class="borderCards" :style="'height: 300px!important; background-image:url(\''+FMS_URL+'/image/fetch/'+item.coverImage+'?tx=resize(h:300)\')!important;background-size:cover!important;'">
	<div style="height:100%!important;width:100%!important;">
		<v-container class="no-padding">
			<v-flex xs12>
				<v-layout align-end justify-start column>
					<v-speed-dial v-model="fab" direction="bottom" open-on-hover transition="slide-y-reverse-transition">
						<template v-slot:activator style="height: 100%!important">
							<v-btn v-model="fab" :color="currentBussinesClub.theme.secondary" dark fab small>
								<v-icon>mdi mdi-dots-vertical</v-icon>
								<v-icon>mdi mdi-close</v-icon>
							</v-btn>
						</template>
						<v-tooltip left>
							<v-btn fab dark small :color="currentBussinesClub.theme.primary" slot="activator" @click="openImageDialog()">
								<v-icon>mdi mdi-pencil</v-icon>
							</v-btn>
							{{$t('message.portfolioImageEdit')}}
						</v-tooltip>
						<v-tooltip left>
							<v-btn fab dark small :color="currentBussinesClub.theme.secondary" slot="activator" @click="openDeleteDialog()">
								<v-icon>mdi mdi-trash-can</v-icon>
							</v-btn>
							{{$t('message.portfolioImageDelete')}}
						</v-tooltip>
						<v-tooltip v-if="item.link" left allow-overflow>
							<v-btn id="userbtn" fab dark small :color="currentBussinesClub.theme.primary" slot="activator" @click="gotoLink()">
								<v-icon>mdi mdi-link</v-icon>
							</v-btn>
							{{$t('message.portfolioImageLink')}}
						</v-tooltip>
					</v-speed-dial>
				</v-layout>
			</v-flex>
		</v-container>
		<div v-if="item.name" class="info-overlay-close">
			<div :title="item.name" style="color:white!important;" class="info-overlay-title">{{item.name}}</div>
			<div class="ellipsis">
				<div>
					<p style="color:white!important;" :title="item.description">{{item.description}}</p>
				</div>
			</div>
		</div>
	</div>
	<v-confirm-dialog v-bind:titleToolbar="$t('message.deleteImage')" ref="deleteImage" type="question" :heading="$t('message.deleteImage')" v-bind:message="$t('message.deleteImageLabel')" @onConfirm="removeImage()"></v-confirm-dialog>
	<v-popup-dialog ref="portfolioImage" :model="portfolioImageDialog" :title="$t('message.portfolioImageEdit')" @onClose="closePortfolioImageDialog" @onSave="savePortfolioImage">
		<v-text-field :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.portfolioImageName')" v-model="item.name"></v-text-field>
		<v-text-field :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.portfolioImageDescription')" v-model="item.description"></v-text-field>
		<v-text-field :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.portfolioImageLinkAddress')" v-model="item.link"></v-text-field>
	</v-popup-dialog>
</v-card>
</template>
<script>
import confirmDialog from "Components/ySocialHome/confirmDialog";
import Vue from "vue";
import {
	mapGetters
} from "vuex";
import AppConfig from "Constants/AppConfig.js";
import FileManagerService from "Services/FileManagerService.js"
export default {
	props: {
		portfolioId: String,
		item: Object,
		index: Number,
	},
	data() {
		return {
			FMS_URL: AppConfig.FMS_URL,
			getObjectPath: Vue.getObjectPath,
			portfolioImageDialog: false,
			fab: false,
			profileTooltip: false,
		};
	},
	computed: {
		...mapGetters(["currentBussinesClub"]),
	},
	components: {
		"v-confirm-dialog": confirmDialog,
	},
	methods: {
		/**
		 * Go to the image link address
		 */
		gotoLink(index) {
			if (!this.item || !this.item.link) {
				return;
			}

			window.open(this.item.link, '_blank');
		},

		/**
		 * Ask to delete the given image
		 */
		openDeleteDialog() {
			this.$refs.deleteImage.openDialog();
		},

		/**
		 * Remove the given portfolio image
		 */
		removeImage() {
			if (!this.item || !this.item._id) {
				this.$refs.deleteImage.close();
				return;
			}

			let portfolio = {
				portfolioId: this.portfolioId,
				images: [this.item._id],
			};

			/* Call the portfolio image delete action */
			this.$store.dispatch('deletePortfolioImage', portfolio).then(() => {
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
				this.$refs.deleteImage.close();
			});
		},

		/**
		 * Open the porfolio image dialog
		 */
		openImageDialog() {
			this.$refs.portfolioImage.doReset();
			this.portfolioImageDialog = true;
		},

		/**
		 * Close the porfolio image dialog
		 */
		closePortfolioImageDialog() {
			this.portfolioImageDialog = false;
		},

		/**
		 * Save the portfolio information
		 */
		savePortfolioImage() {
			if (!this.$refs.portfolioImage.doValidate()) {
				return;
			}

			let portfolioImage = {
				portfolioId: this.portfolioId,
				imageId: this.item._id,
				name: this.item.name,
				description: this.item.description,
				link: this.item.link,
			};

			/* Call the portfolio image action */
			this.$store.dispatch('updatePortfolioImage', portfolioImage).then(() => {
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
				this.closePortfolioImageDialog();
			});
		},
	}
};
</script>
<style scoped>
.info-overlay-close {
	background-color: rgba(0, 0, 0, .6) !important;
	position: absolute;
	bottom: 0;
	width: 100% !important;
	height: 25px !important;
	overflow: hidden !important;
}

.info-overlay-close:hover {
	background-color: rgba(0, 0, 0, .6) !important;
	position: absolute;
	bottom: 0;
	width: 100% !important;
	height: 75px !important;
}


.info-overlay-title {
	margin: 0 10px !important;
	white-space: nowrap !important;
	overflow: hidden !important;
	text-overflow: ellipsis !important;
	line-height: 25px;
	font-weight: 700;
}

.ellipsis {
	overflow: hidden;
	white-space: normal;
	height: 50px;
	line-height: 25px;
	margin: 0 10px;
}

.ellipsis:before {
	content: "";
	float: left;
	width: 5px;
	height: 50px;
}

.ellipsis>*:first-child {
	float: right;
	width: 100%;
	margin-left: -5px;
}

.ellipsis:after {
	content: "\02026";
	box-sizing: content-box;
	-webkit-box-sizing: content-box;
	-moz-box-sizing: content-box;
	float: right;
	position: relative;
	top: -25px;
	left: 100%;
	width: 3em;
	margin-left: -3em;
	padding-right: 5px;
	text-align: right;
}
</style>
