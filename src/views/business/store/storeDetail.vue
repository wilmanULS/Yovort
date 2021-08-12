<template>
<v-layout row wrap mt-2 v-if="storeDetail!=null">
	<v-flex xs12>
		<span class="grey--text vb-cp-subtitle">{{$t('message.vBusinessCpBusinessInfoDetail')}}</span>
	</v-flex>
	<v-flex xs12 mt-1>
		<span class="vb-cp-title">{{$t("message.vStoreDetailType")}}</span>
	</v-flex>
	<v-radio-group v-model="storeDetail.type" :mandatory="false">
		<v-radio :color="currentBussinesClub.theme.primary" label="Tienda Virtual" :value="0"></v-radio>
	</v-radio-group>
	<v-flex xs12 mt-1>
		<span class="vb-cp-title">{{$t('message.vStoreDetailInformation')}}</span>
	</v-flex>
	<v-flex xs12 mt-1>
		<v-text-field :rules="nameRules" v-model="storeDetail.name[0].name" :class="{'vb-mr': $vuetify.breakpoint.mdAndUp, 'mb-1':$vuetify.breakpoint.smAndDown }" :color="currentBussinesClub.theme.primary"
		 :label="$t('message.vStoreDetailInformationName')" clearable required hide-details></v-text-field>
	</v-flex>
	<v-flex xs12 mt-1>
		<span style="text-align: left;padding-bottom: 5px" class="titleCard darker-titles">{{$t('message.vStoreDetailInformationDS')+"(*)"}}</span>
		<vue-editor v-model="textEditorDescriptionShort" :editorToolbar="customToolbar"></vue-editor>
	</v-flex>

	<v-flex xs12 mt-1>
		<span style="text-align: left;padding-bottom: 5px" class="titleCard darker-titles">{{$t('message.vStoreDetailInformationDL') +"(*)"}}</span>
		<vue-editor v-model="textEditorDescriptionLarge" :editorToolbar="customToolbar"></vue-editor>
	</v-flex>
	<v-flex xs12 mt-1>
		<span style="text-align: left;padding-bottom: 5px" class="titleCard darker-titles">{{$t('message.vStoreDetailInformationDD')+"(*)"}}</span>
		<vue-editor v-model="textEditorDescriptionDetailed" :editorToolbar="customToolbar"></vue-editor>
	</v-flex>

	<!-- -->
	<v-flex xs12 mt-1>
		<span class="vb-cp-title">{{"Redes Sociales"}}</span>
	</v-flex>
	<v-flex xs12 md6 class="profile-flex-padding" mt-1>
		<v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp}" id="demo" :color="currentBussinesClub.theme.primary" prepend-inner-icon="mdi mdi-facebook" v-bind:label="$t('message.accountFb')" v-model="storeDetail.socialNetworks.facebook"
		 hide-details></v-text-field>
	</v-flex>
	<v-flex xs12 md6 class="profile-flex-padding" mt-1>
		<v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp}" id="demo" :color="currentBussinesClub.theme.primary" prepend-inner-icon="mdi mdi-twitter" v-bind:label="$t('message.accountTw')" v-model="storeDetail.socialNetworks.twitter"
		 hide-details></v-text-field>
	</v-flex>
	<v-flex xs12 md6 class="profile-flex-padding" mt-1>
		<v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp}" id="demo" :color="currentBussinesClub.theme.primary" prepend-inner-icon="mdi mdi-youtube" v-bind:label="$t('message.accountYt')" v-model="storeDetail.socialNetworks.youtube"
		 hide-details></v-text-field>
	</v-flex>
	<v-flex xs12 md6 class="profile-flex-padding" mt-1>
		<v-text-field :class="{'mr05': $vuetify.breakpoint.mdAndUp}" id="demo" :color="currentBussinesClub.theme.primary" prepend-inner-icon="mdi mdi-vimeo" v-bind:label="$t('message.accountVm')" v-model="storeDetail.socialNetworks.vimeo"
		 hide-details></v-text-field>
	</v-flex>
	<v-layout mt-1 align-end justify-end row fill-height>
		<v-btn class="rounded-buttons" dark @click="save" :color="currentBussinesClub.theme.primary">{{$t('message.save')}}</v-btn>
	</v-layout>
</v-layout>
</template>

<script>
import {
	mapGetters
} from "vuex";
import Vue from "vue";
import {
	VueEditor
} from "vue2-editor";

export default {
	components: {
		VueEditor
	},
	computed: {
		...mapGetters([
			"currentBussinesClub",
			"businessSelected",
			"businessId",
			"getStoreById"
		])
	},
	data() {
		return {
			getObjectPath: Vue.getObjectPath,
			storePattern: null,
			storeDetail: null,
			radios: 0,
			textEditorDescriptionShort: "",
			textEditorDescriptionLarge: "",
			textEditorDescriptionDetailed: "",
			customToolbar: [
				["bold", "italic", "underline"],
				[{
						align: ""
					},
					{
						align: "right"
					},
					{
						align: "justify"
					},
					{
						align: "center"
					}
				],
				[{
					list: "ordered"
				}, {
					list: "bullet"
				}, {
					list: "check"
				}],
				[{
					indent: "-1"
				}, {
					indent: "+1"
				}]
			],
			configEditText: {
				toolbar: [
					"removeFormat",
					"|",
					"elements",
					"fontName",
					"fontSize",
					"foreColor",
					"divider",
					"bold",
					"italic",
					"underline",
					"links",
					"divider",
					"justifyLeft",
					"justifyCenter",
					"justifyRight",
					"justifyFull",
					"|",
					"indent",
					"outdent",
					"insertOrderedList",
					"insertUnorderedList"
				],
				fontName: [{
						val: "arial black"
					},
					{
						val: "times new roman"
					},
					{
						val: "Courier New"
					}
				],
				fontSize: [
					"12px",
					"14px",
					"16px",
					"18px",
					"20px",
					"24px",
					"28px",
					"32px",
					"36px"
				],

				uploadUrl: "",
				classList: ["color:blue"]
			},
			required: "",
			nameRules: null
		};
	},

	created() {
		this.$nextTick(() => {
			this.required = this.$t("message.required");
			this.nameRules = [v => !!v || this.required];
		});
	},
	mounted() {
		this.$nextTick(() => {
			this.storePattern = this.getStoreById;
			this.storeDetail = this.getStoreById.stores[0];
			this.textEditorDescriptionShort = this.storeDetail.name[0].description.short;
			this.textEditorDescriptionLarge = this.storeDetail.name[0].description.long;
			this.textEditorDescriptionDetailed = this.storeDetail.name[0].description.detail;
		});
	},
	methods: {
		/*  initEditors() {
		  this.textEditorDescriptionShort.setContent(
		    this.storeDetail.name[0].description.short
		  );
		  this.textEditorDescriptionLarge.setContent(
		    this.storeDetail.name[0].description.long
		  );
		  this.textEditorDescriptionDetailed.setContent(
		    this.storeDetail.name[0].description.detail
		  );
		}, */
		save() {
			let socialNetWork = {
				storeData: {
					_id: this.storePattern._id,
					storesId: this.storeDetail._id,
					socialNetworks: {
						facebook: this.storeDetail.socialNetworks.facebook,
						twitter: this.storeDetail.socialNetworks.twitter,
						youtube: this.storeDetail.socialNetworks.youtube,
						vimeo: this.storeDetail.socialNetworks.vimeo
					}
				},
				type: "Social_Network"
			};
			let type = {
				storeData: {
					_id: this.storePattern._id,
					storesId: this.storeDetail._id,
					type: this.storeDetail.type
				},
				type: "Type"
			};
			this.storeDetail.name[0].description.short = this.textEditorDescriptionShort;
			this.storeDetail.name[0].description.long = this.textEditorDescriptionLarge;
			this.storeDetail.name[0].description.detail = this.textEditorDescriptionDetailed;
			let names = {
				storeData: {
					_id: this.storePattern._id,
					storesId: this.storeDetail._id,
					name: this.storeDetail.name[0].name,
					description: this.storeDetail.name[0].description,
					lang: 7,
					namesId: this.storeDetail.name[0]._id
				},
				type: "Name"
			};
			this.$store
				.dispatch("updateStoreDetail", names)
				.then(() => {
					this.$store.dispatch("updateStoreDetail", type).then(() => {
						this.$store
							.dispatch("updateStoreDetail", socialNetWork)
							.then(res => {
								this.$store.dispatch("setBusinessStore", res.data.data);
								this.$store.dispatch("doNotification", {
									msg: this.$t("message.infoUpdate"),
									type: "success"
								});
							});
					});
				})
				.catch(() => {
					this.$store.dispatch("doNotification", {
						msg: this.$t("message.sessionErrorDefault"),
						type: "error"
					});
				});
		}
	}
};
</script>

<style>
</style>
