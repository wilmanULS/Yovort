<template>
<v-container class="no-padding" lazy>
	<v-layout row wrap>
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
													<v-icon>add</v-icon>
												</v-btn>
											</div>
											<span class="textAddSkill">{{$t('message.addSkillbook')}}</span>
										</v-flex>
									</v-layout>
								</v-container>
							</v-card>
						</v-flex>
						<v-flex xs12 md3 v-for="(item, index) in userProfile.briefcase" :key="index">
							<v-card class="borderCards" :style="'height: 300px!important;' + (item.images.length>0?'background-image:url(\''+FMS_URL+'/image/fetch/'+item.images[0].coverImage+'?tx=resize(w:300,h:300)\')!important;': '')">
								<div style="height:100%!important;width:100%!important;background-color:rgb(44, 53, 58,.7)!important;">
									<v-container class="no-padding">
										<v-flex xs12>
											<v-layout align-end justify-start column>
												<v-btn class="main-title" flat icon small>
													<v-icon small color="#BDBDBD">edit</v-icon>
												</v-btn>
												<v-btn class="main-title" flat icon small>
													<v-icon small color="#BDBDBD">delete</v-icon>
												</v-btn>
											</v-layout>
										</v-flex>
										<v-layout>
											<v-flex xs12 text-xs-center class="pt-4">
												<div>
													<v-btn small outline fab color="white">
														<v-icon small class="pb-1" color="#fff">fas fa-briefcase</v-icon>
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
	</v-layout>
	<v-confirm-dialog v-bind:titleToolbar="$t('message.deleteImage')" ref="deleteImage" type="question" :heading="$t('message.deleteImage')" v-bind:message="$t('message.deleteImageLabel')" @onConfirm="beforeRemoveImage()"></v-confirm-dialog>
	<v-popup-dialog ref="portfolio" :model="portfolioDialog" :title="indexPortfolio<0?$t('message.addSkillbookTitle'):$t('message.editSkillbookTitle')" showRequired @onClose="closePortfolioDialog" @onSave="savePortfolio()">
		<v-text-field ref="namePortfolio" :color="currentBussinesClub.theme.primary" v-bind:label="$t('message.nameSkillbookLabel')+'*'" v-model="portfolioName" :rules="imageNameRules"></v-text-field>
	</v-popup-dialog>
</v-container>
</template>
<script>
import confirmDialog from "Components/ySocialHome/confirmDialog";
import Vue from "vue";
import {
	mapGetters
} from "vuex";
import AppConfig from "Constants/AppConfig.js";
import {
	setTimeout
} from "timers";
export default {
	props: {
		currentTab: Number
	},
	data() {
		return {
			validImgParams: true,
			FMS_URL: AppConfig.FMS_URL,
			path: "",
			file: "",
			arrayImages: [],
			arrayInformationImage: [],
			nameImage: "",
			descriptionImage: "",
			urlImage: "",
			portfolioName: "",
			index: "",
			done: "",
			fileList: "",
			multipleImages: false,
			dataProyect: false,
			getObjectPath: Vue.getObjectPath,
			portfolioDialog: false,
			indexPortfolio: -1,
			hasImage: false,
			image: "",
			required: "",
			maxLengthText: "",
			imageNameRules: [],
			descriptionImageRules: [],

			/*** */
			postAction: AppConfig.FMS_URL + "/file/upload",
			filesImage: []
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
	},
	created() {
		this.required = this.$t("message.required");
		this.maxLengthText = this.$t("message.maxLengthText");
		this.imageNameRules = [v => !!v || this.required];
		this.descriptionImageRules = [v => !!v || this.required];
	},
	methods: {
		/* openDeleteImageDialog(index, done, fileList) {
		  this.index = index;
		  this.done = done;
		  this.fileList = fileList;
		  this.$refs.deleteImage.openDialog();
		},
		beforeRemoveImage() {
		  this.done();
		  this.$refs.deleteImage.close();
	  },*/

		savePortfolio() {
			let newPortfolio = {
				name: this.portfolioName,
				images: []
			};
			this.$store.dispatch("addNewPortfolio", newPortfolio).then(() => {
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
		},
		/*uploadImageSuccess(formData, index, fileList) {
		  this.nameImage = "";
		  this.descriptionImage = "";
		  this.urlImage = "";

		  this.file = this.dataUrltoFile(
		    fileList[index].path,
		    fileList[index].name
		  );

		  this.dataProyect = true;
		  this.multipleImages = false;
		},
		saveImage() {
		  if (this.$refs.form.validate()) {
		    this.$store
		      .dispatch("uploadImageFMS", this.file)
		      .then(res => {
		        this.path = res.data.data.path;
		        this.arrayImages = {
		          name: this.nameImage,
		          description: this.descriptionImage,
		          coverImage: this.path,
		          link: this.urlImage
		        };
		        this.arrayInformationImage.push(this.arrayImages);
		      })
		      .finally(() => {
		        this.nameImage = "";
		        this.descriptionImage = "";
		        this.urlImage = "";
		      });
		    this.multipleImages = true;
		    this.dataProyect = false;
		  }
		}, */
		/**transform base 64 to upload file */
		dataUrltoFile(dataurl, filename) {
			var arr = dataurl.split(",");
			var mime = arr[0].match(/:(.*?);/)[1];
			var bstr = atob(arr[1]);
			var n = bstr.length,
				u8arr = new Uint8Array(n);
			while (n--) {
				u8arr[n] = bstr.charCodeAt(n);
			}
			return new File([u8arr], filename, {
				type: mime
			});
		},
		openPortfolioDialog(index) {
			this.indexPortfolio = index;
			this.portfolioDialog = true;
		},
		closePortfolioDialog() {
			this.portfolioDialog = false;
		}
	}
};
</script>
<style>
.image-container {
	width: 100% !important;
	height: 210px !important;
	border: 1px dashed #d6d6d6;
	border-radius: 4px;
	background-color: #fff;
}

.show-img {
	max-height: 120px !important;
	max-width: 180px !important;
	display: block;
	vertical-align: middle;
}

@media screen and (max-width: 600px) {
	.show-img {
		max-height: 130px !important;
		max-width: 230px !important;
		display: block;
		vertical-align: middle;
	}
}

.textColor {
	color: black !important;
}

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

#overlay {
	width: 100%;
	height: 100%;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	background-color: rgba(0, 0, 0, 0.6);
	z-index: 2;
}
</style>
