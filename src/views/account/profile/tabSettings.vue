<template>
<v-form onSubmit="return false;" ref="form" v-model="valid" lazy-validation lazy>
	<div>
		<v-container grid-list-xs class="no-padding">
			<v-layout row wrap>
				<v-flex xs12 md6>
					<v-card flat color="transparent" :class="{'mr05': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }">
						<h4 class="titleDivider pl-1 darker-titles">{{$t('message.changePassword')}}</h4>
						<v-divider class="styleDivider"></v-divider>
						<v-flex xs12>
							<v-layout row wrap>
								<v-flex xs12 sm12 md12 lg12 class="profile-flex-padding">
									<v-text-field browser-autocomplete="off" autocomplete="off" ref="pass" :append-icon="show1 ? 'mdi mdi-eye' : 'mdi mdi-eye-off'" :color="currentBussinesClub.theme.primary" :type="show1 ? 'text' : 'password'"
									 name="input-10-1" v-bind:label="$t('message.confirmCurrentPassword')" hint="At least 8 characters" hide-details v-model="passwordOld" @click:append="show1 = !show1"></v-text-field>
								</v-flex>
								<v-flex xs12 sm6 md6 lg6 class="profile-flex-padding">
									<v-text-field class="force-bottom-border dark_field" :class="{'mr05': $vuetify.breakpoint.mdAndUp}" ref="newPass" browser-autocomplete="off" autocomplete="off"
									 :append-icon="show2 ? 'mdi mdi-eye' : 'mdi mdi-eye-off'" :color="currentBussinesClub.theme.primary" :type="show2 ? 'text' : 'password'" name="input-10-1" v-bind:label="$t('message.newPassword')"
									 :hint="strengthNewPassword" persistent-hint v-model="passwordNew" @click:append="show2 = !show2" loading>
										<template v-slot:progress>
											<v-progress-linear :value="progress" :color="checkPasswordStrength" height="3"></v-progress-linear>
										</template>
									</v-text-field>
								</v-flex>
								<v-flex xs12 sm6 md6 lg6 class="profile-flex-padding">
									<v-text-field class="force-bottom-border dark_field" :class="{'ml05': $vuetify.breakpoint.mdAndUp}" ref="matchPass" browser-autocomplete="off" autocomplete="off"
									 :append-icon="show3 ? 'mdi mdi-eye' : 'mdi mdi-eye-off'" :color="currentBussinesClub.theme.primary" :type="show3 ? 'text' : 'password'" name="input-10-1" v-bind:label="$t('message.confirmPassword')"
									 v-model="passwordConfirm" @click:append="show3 = !show3" loading :hint="strengthConfirmPassword" persistent-hint>
										<template v-slot:progress>
											<v-progress-linear :value="progressConfirm" :color="checkPasswordMatch" height="3"></v-progress-linear>
										</template>
									</v-text-field>
								</v-flex>
							</v-layout>
						</v-flex>
						<v-layout align-end justify-end row>
							<v-flex xs12 md2 lg2 class="profile-flex-padding">
								<v-btn @click="clearFields" dark class="rounded-buttons" :color="currentBussinesClub.theme.secondary" block large>{{$t('message.restartAll')}}</v-btn>
							</v-flex>
							<v-flex xs12 md2 lg2 class="profile-flex-padding">
								<v-btn @click="saveSettingProfile" :disabled="!(this.passwordOld.length>0 && this.newValid && this.newMatch)" :dark="(this.passwordOld.length>0 && this.newValid && this.newMatch)" class="rounded-buttons"
								 :color="currentBussinesClub.theme.primary" large block>{{$t('message.saveChanges')}}</v-btn>
							</v-flex>
						</v-layout>
					</v-card>
				</v-flex>
				<v-flex xs12 md6>
					<v-card flat color="transparent" :class="{'ml05': $vuetify.breakpoint.mdAndUp}">
						<h4 class="titleDivider pl-1 darker-titles">{{$t('message.configSkillBook')}}</h4>
						<v-divider class="styleDivider"></v-divider>
						<v-layout row wrap>
							<v-flex xs6 pt-3 pl-2>
								<span class="textBlack pb-0">{{$t('message.skillsBookPrivate')}}</span>
							</v-flex>
							<v-layout align-end justify-end row>
								<v-flex xs3 md2>
									<v-switch @change="switchSkillbookVisibility" :color="currentBussinesClub.theme.primary" v-model="skillsbookVisibility"></v-switch>
								</v-flex>
							</v-layout>
						</v-layout>
						<v-layout row wrap pt-0>
							<v-flex xs6 pt-3 pl-2>
								<span class="textBlack pb-0">{{$t('message.animation')}}</span>
							</v-flex>
							<v-layout align-end justify-end row>
								<v-flex xs3 md2>
									<v-switch @change="animationActive()" :color="currentBussinesClub.theme.primary" v-model="animation"></v-switch>
								</v-flex>
							</v-layout>
						</v-layout>
						<v-layout row wrap pt-0>
							<v-card-title class="pl-2 pt-0">
								<span class="textBlack">{{$t('message.backgroundImage')}}</span>
							</v-card-title>
							<v-flex xs12 pl-2>
								<v-select @change="getImages(categorySkill)" :items="getCategoryImage" item-text="name" item-value="id" :color="currentBussinesClub.theme.primary" :no-data-text="$t('message.nodatafound')" v-model="categorySkill"
								 v-bind:label="$t('message.selectCategory')"></v-select>
							</v-flex>
						</v-layout>
						<v-container>
							<v-card flat color="transparent">
								<carousel-3d ref="carousel" :startIndex="3" :perspective="0" :space="220" :display="5" :width="200" :height="120">
									<slide v-bind:id="index+'image'" :index="index" v-for="(slide,index) in this.vecImages" :key="index" :v-model="index" v-bind:style="index==currentImage?'border: 4px solid rgb(156, 40, 178)!important;':''">
										<v-img :src="`${FMS_URL}/image/fetch/${slide}?tx=resize(w:200,h:120)`">
											<template v-slot:placeholder>
												<v-layout fill-height align-center justify-center ma-0>
													<v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
												</v-layout>
											</template>
										</v-img>
									</slide>
								</carousel-3d>
								<v-layout align-center justify-center row>
									<v-flex xs12 md3>
										<v-btn :disabled="btnSelect" @click="selectedImage()" dark class="rounded-buttons" :color="currentBussinesClub.theme.primary" large block>{{$t('message.select')}}</v-btn>
									</v-flex>
								</v-layout>
							</v-card>
						</v-container>
					</v-card>
				</v-flex>
			</v-layout>
		</v-container>
	</div>
</v-form>
</template>
<script>
import {
	mapGetters
} from "vuex";
import ERRORS from "../../../constants/errors";
import AppConfig from "../../../constants/AppConfig.js";
import Vue from "vue";
export default {
	watch: {
		userProfile(value) {
			this.reloadDataSkillBook();
		},
	},
	props: {
		currentTab: Number
	},
	data() {
		return {
			isActive: {},
			currentImage: "",
			getObjectPath: Vue.getObjectPath,
			index: "",
			btnSelect: true,
			vecImages: [],
			categorySkill: "",
			animation: true,
			skillsbookVisibility: false,
			strengthNewPassword: "",
			strengthConfirmPassword: "",
			valid: true,
			selected: null,
			show1: false,
			show2: false,
			show3: false,
			show4: false,
			passwordOld: "",
			passwordNew: "",
			passwordConfirm: "",
			matchedPassword: false,
			newValid: false,
			newMatch: false,
			FMS_URL: AppConfig.FMS_URL
		};
	},
	computed: {
		...mapGetters([
			"userProfile",
			"currentBussinesClub",
			"getCategoryImage",
			"images"
		]),
		userProfile: {
			get() {
				if (this.$store.getters.userProfile != "")
					return JSON.parse(this.$store.getters.userProfile);
			}
		},
		progressConfirm() {
			return Math.min(100, this.passwordConfirm.length * 12);
		},
		progress() {
			return Math.min(100, this.passwordNew.length * 12);
		},
		checkPasswordStrength() {
			const pattern = /(?=.*?[A-Z])(?=.*?[a-z])(?=.*?[0-9]).{8,}$/;
			if (this.passwordNew == "") {
				this.newValid = false;
				this.strengthNewPassword = "";
				return [""][0];
			} else {
				if (pattern.test(this.passwordNew)) {
					this.newValid = true;
					this.strengthNewPassword = this.$t("message.strengthPasswordValid");
					return ["success"][Math.floor(this.progress / 110)];
				} else {
					this.newValid = false;
					this.strengthNewPassword = this.$t("message.strengthPasswordWeak");
					return ["error"][Math.floor(this.progress / 110)];
				}
			}
		},
		checkPasswordMatch() {
			if (this.passwordConfirm == "") {
				this.newMatch = false;
				this.strengthConfirmPassword = "";
				return [""][0];
			} else {
				if (this.passwordConfirm === this.passwordNew) {
					this.newMatch = true;
					this.strengthConfirmPassword = null;
					return ["success"][Math.floor(this.progressConfirm / 110)];
				} else {
					this.newMatch = false;
					this.strengthConfirmPassword = this.$t("message.passwordMismatch");
					return ["error"][Math.floor(this.progressConfirm / 110)];
				}
			}
		}
	},
	created() {
		this.$store.dispatch("getCategory").finally(() => {
			this.reloadDataSkillBook();
		});
	},
	methods: {
		reloadDataSkillBook() {
			if (this.categorySkill == "") {
				this.categorySkill = localStorage.getItem('categorySkill')
				this.getImages(this.categorySkill)
			}
			this.animation = this.getObjectPath(this.userProfile, 'setting.skillbook.animation', true)
			this.skillsbookVisibility = this.getObjectPath(this.userProfile, 'setting.skillbook.private', false)
			this.currentImage = this.getObjectPath(this.userProfile, 'setting.skillbook.wallpaper', '')
		},
		animationActive() {
			const dataSettings = {
				field: "animation",
				value: this.animation
			};
			this.$store.dispatch("updateProfileSkillsbook", dataSettings)
				.then(data => {
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
		switchSkillbookVisibility() {
			const dataSettings = {
				field: "private",
				value: this.skillsbookVisibility,
			};
			this.$store.dispatch("updateProfileSkillsbook", dataSettings)
				.then(data => {
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
		selectedImage() {
			this.currentImage = this.$refs.carousel.currentIndex
			let dataSettings = {
				field: "wallpaper",
				value: this.vecImages[this.currentImage]
			};
			this.$store.dispatch("updateProfileSkillsbook", dataSettings)
				.then(data => {
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
		getImages(codeCategory) {
			localStorage.setItem('categorySkill', codeCategory);
			this.$root.$children[0].$emit(
				"showLoading",
				"Cargando galería de imagenes.."
			);
			this.$store
				.dispatch("getAllImagesByCategory", codeCategory)
				.then(res => {
					this.vecImages = res.data.data;

					this.$refs.carousel.setSize();
					this.$refs.carousel.goSlide(0);

				})
				.finally(() => {
					this.$root.$children[0].$emit("hideLoading");
					this.btnSelect = false;
				});
		},
		clearFields() {
			this.newValid = false;
			this.newMatch = false;
			this.matchedPassword = false;
			this.strengthNewPassword = "";
			this.strengthConfirmPassword = "";
			this.passwordConfirm = "";
			this.passwordNew = "";

			this.$refs.pass.reset();
			this.$refs.newPass.reset();
			this.$refs.matchPass.reset();
			this.$refs.form.reset();
		},
		saveSettingProfile() {
			const password = {
				oldPassword: this.passwordOld,
				password: this.passwordNew,
				appPayload: {
					typeProfile: "Configuración de Cuenta"
				}
			};
			this.$store
				.dispatch("updatePassword", password)
				.then(updatePassword => {
					this.$store.dispatch("doNotification", {
						msg: this.$t("message.messageUpdateSucces"),
						type: "success"
					});
				})
				.catch(err => {
					if (err.error == ERRORS.IAM.INVALID_PASSWORD) {
						this.$store.dispatch("doNotification", {
							msg: this.$t("message.currentPasswordNotMatch"),
							type: "error"
						});
						return;
					}

					this.$store.dispatch("doNotification", {
						msg: this.$t("message.sessionErrorDefault"),
						type: "error"
					});
				})
				.finally(() => {
					this.passwordNew = "";
					this.passwordConfirm = "";
					this.passwordOld = "";
				});
		},
		changeColor() {
			this.show1 = !this.show1;
			if (this.show1) {}
		}
	}
};
</script>
<style scoped>
/* .borderCarousel{

    border:5px solid rgb(156, 40, 178) !important;
} */

.textBlack {
	color: black !important;
}

.styleDivider {
	margin-left: 5px;
	margin-right: 5px;
	padding-bottom: 2px;
}

.force-bottom-border>>>.v-input__slot {
	border-bottom: 1px solid rgba(0, 0, 0, 0.42) !important;
}
</style>
