<template>
<div lazy>
	<div v-if="getBusinessClubRole">
		<v-layout row wrap pl-2 pr-2 pb-0>
			<v-flex xs12 sm12 md12 lg6 xl6>
				<v-card flat :class="{'mr05': $vuetify.breakpoint.mdAndUp}">
					<v-card-actions>
						<span v-bind:style="{'color':currentBussinesClub.theme.primary}">{{$t('message.description')}}</span>
						<v-scroll-x-transition>
							<div style="position:absolute;right:0px" v-if="editingDescriptionClub">
								<v-btn small @click="updateDescription()" flat :color="currentBussinesClub.theme.secondary" icon class="ma-0 pa-0">
									<v-icon small>check</v-icon>
								</v-btn>
								<v-btn class="ma-0 pa-0" small flat @click="cancelUpdateDescription()" :color="currentBussinesClub.theme.warning" icon>
									<v-icon small>close</v-icon>
								</v-btn>
							</div>
						</v-scroll-x-transition>
					</v-card-actions>
					<v-card :class="$vuetify.breakpoint.smAndDown?'mobileEditor':'desktopEditor'" flat style="border-style: solid; border-width: 1px; border-color: #cfd8dc;">
						<div id="editorContainer1" ref="textDescriptionEditor"></div>
					</v-card>
				</v-card>
			</v-flex>
			<v-flex lg6>
				<v-card flat :class="{'ml05': $vuetify.breakpoint.mdAndUp}">
					<v-card-actions v-bind:style="{'color':currentBussinesClub.theme.primary}">
						{{$t('message.rules')}}
						<v-scroll-x-transition>
							<div style="position:absolute;right:0px" v-if="editingRule ||addingRule ">
								<v-btn small @click="addOrEditRule()" flat :color="currentBussinesClub.theme.secondary" icon class="ma-0 pa-0">
									<v-icon small>check</v-icon>
								</v-btn>
								<v-btn class="ma-0 pa-0" small flat @click="cancelEditRule()" :color="currentBussinesClub.theme.warning" icon>
									<v-icon small>close</v-icon>
								</v-btn>
							</div>
						</v-scroll-x-transition>
					</v-card-actions>
					<v-card :class="$vuetify.breakpoint.smAndDown?'mobileEditor':'desktopEditor'" flat style="border-style: solid; border-width: 1px; border-color: #cfd8dc;">
						<div id="editorContainer2" ref="rulesEditor"></div>
					</v-card>
				</v-card>
			</v-flex>
		</v-layout>
		<v-layout pl-2 pr-2 row wrap v-if="mountedReady">
			<v-flex mb-2 xs12 sm12 md12 lg12 xl12>
				<v-card flat>
					<v-card-actions>
						<span v-bind:style="{'color':currentBussinesClub.theme.primary}">{{$t('message.preview')}}</span>
					</v-card-actions>
					<v-card flat class="py-3 pl-3" style="border-style: solid; border-width: 1px; border-color: #cfd8dc;">
						<v-layout row wrap pl-4>
							<p>
								<span v-html="descriptionClub">
								</span>
							</p>
						</v-layout>
						<ol v-if="rulesClub.length>0">
							<li class="custom-li" v-for="(rule, i) in rulesClub" :key="i">
								<v-layout py-2 row wrap :style="editing[i]&&editing[i].state?'background-color:'+currentBussinesClub.theme.primary+';opacity:0.7':''">
									<v-flex hidden-xs-only xs11>
										<span :style="editing[i]&&editing[i].state?'color:white':'color:black'" v-html="rule.descriptionRule"></span>
									</v-flex>
									<v-flex hidden-xs-only xs1>
										<v-card color="transparent" flat class="ma-0 pa-0">
											<v-scroll-x-transition>
												<div v-if="removing[i]&&!removing[i].state" style="position:absolute; left:20px">
													<v-btn small @click="removing[i].state=true" flat :color="editing[i]&&!editing[i].state?currentBussinesClub.theme.secondary:'#ffffff'" icon class="ma-0 pa-0">
														<v-icon small>delete</v-icon>
													</v-btn>
													<v-btn class="ma-0 pa-0" small flat @click="editRule(rule,i)" :color="editing[i]&&!editing[i].state?currentBussinesClub.theme.warning:'#ffffff'" icon>
														<v-icon small>edit</v-icon>
													</v-btn>
												</div>
											</v-scroll-x-transition>
											<v-scroll-x-transition>
												<div v-if="removing[i]&&removing[i].state" style="position:absolute;left:20px">
													<v-btn :color="editing[i]&&!editing[i].state?currentBussinesClub.theme.secondary:'#ffffff'" flat @click="deleteRule(rule._id,i)" icon small class="ma-0 pa-0">
														<v-icon small>check</v-icon>
													</v-btn>
													<v-btn :color="editing[i]&&!editing[i].state?currentBussinesClub.theme.primary:'#ffffff'" flat @click.stop="removing[i].state=false" icon small class="ma-0 pa-0">
														<v-icon small>clear</v-icon>
													</v-btn>
												</div>
											</v-scroll-x-transition>
										</v-card>

									</v-flex>
									<v-flex hidden-sm-and-up xs9>
										<span style="color:black" v-html="rule.descriptionRule"></span>
									</v-flex>
									<v-flex hidden-sm-and-up xs3>
										<v-card color="transparent" flat class="ma-0 pa-0">
											<v-scroll-x-transition>
												<div style="position:absolute;bottom:-6.5px" v-if="removing[i]&&!removing[i].state">
													<v-btn small @click="removing[i].state=true" flat :color="editing[i]&&!editing[i].state?currentBussinesClub.theme.secondary:'#ffffff'" icon class="ma-0 pa-0">
														<v-icon small>delete</v-icon>
													</v-btn>
													<v-btn class="ma-0 pa-0" small flat @click="editRule(rule,i)" :color="editing[i]&&!editing[i].state?currentBussinesClub.theme.warning:'#ffffff'" icon>
														<v-icon small>edit</v-icon>
													</v-btn>
												</div>
											</v-scroll-x-transition>
											<v-scroll-x-transition>
												<div style="position:absolute;bottom:-6.5px" v-if="removing[i]&&removing[i].state">
													<v-btn :color="editing[i]&&!editing[i].state?currentBussinesClub.theme.secondary:'#ffffff'" flat @click="deleteRule(rule._id,i)" icon class="ma-0 pa-0" small>
														<v-icon small>check</v-icon>
													</v-btn>
													<v-btn :color="editing[i]&&!editing[i].state?currentBussinesClub.theme.primary:'#ffffff'" flat @click.stop="removing[i].state=false" icon class="ma-0 pa-0" small>
														<v-icon small>clear</v-icon>
													</v-btn>
												</div>
											</v-scroll-x-transition>
										</v-card>
									</v-flex>
								</v-layout>
							</li>
						</ol>
						<v-layout v-else align-center justify-center row fill-height>
							<span>{{$t('message.noRulesDataMessage')}}</span>
						</v-layout>
					</v-card>
				</v-card>
			</v-flex>
		</v-layout>
	</div>
	<div v-else>
		<v-layout row wrap>
			<v-flex xs12>
				<span class="mb-2" v-html="descriptionClub">
				</span>
			</v-flex>
		</v-layout>
		<v-layout row wrap>
			<v-flex xs12>
				<ol v-if="rulesClub.length>0">
					<li class="custom-li" v-for="(rule, i) in rulesClub" :key="i">
						<v-layout py-2 row wrap>
							<v-flex xs12>
								<span v-html="rule.descriptionRule" style="color:black;"></span>
							</v-flex>
						</v-layout>
					</li>
				</ol>
			</v-flex>
		</v-layout>
	</div>
</div>
</template>
<script>
import Vue from 'vue';
import {
	createEditor
} from "vueditor";
import {
	mapGetters
} from "vuex";
import {
	watch
} from "fs";
export default {
	data() {
		return {
			textEditorDescription: null,
			textEditorRules: null,
			selectedRule: null,
			mountedReady: false,
			editingRule: false,
			addingRule: false,
			editingDescriptionClub: false,
			textEditor1: null,
			editDescription: false,
			inputRule: "",
			addLoading: false,
			removing: [],
			editing: [],
			configEditText: {
				toolbar: [
					"removeFormat",
					"undo",
					"|",
					"elements",
					"fontName",
					"fontSize",
					"foreColor",
					"backColor",
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
				classList: ['color:blue']
			}
		};
	},
	computed: {
		...mapGetters([
			"userProfile",
			"currentBussinesClub",
			"rulesClub",
			"descriptionClub",
			"getBusinessClubRole"
		])
	},
	mounted() {
		Vue.nextTick(() => {
			this.initRules();
			if (this.getBusinessClubRole) {
				this.textEditorRules = createEditor(
					"#editorContainer2",
					this.configEditText
				);
				this.$watch(
					() => {
						return this.textEditorRules.getContent();
					},
					val => {
						if (!this.editingRule && this.mountedReady && val != "") {
							this.addingRule = true;
						}
					}
				);
			}
		});
	},
	methods: {
		initRules() {
			this.removing = [];
			this.$store
				.dispatch("getRules", this.currentBussinesClub.clubId)
				.then(res => {
					if (this.getBusinessClubRole) {
						for (let i = 0; i < this.rulesClub.length; i++) {
							this.removing.push({
								state: false
							});
							this.editing.push({
								state: false
							});
						}

						this.textEditorRules.setContent("");
						this.mountedReady = true;
						this.editingRule = false;
						this.addingRule = false;
					}
					this.$store
						.dispatch("getDescriptionClub", this.currentBussinesClub.clubId)
						.then(res => {
							if (this.getBusinessClubRole && this.textEditorDescription == null) {
								this.textEditorDescription = createEditor(
									"#editorContainer1",
									this.configEditText
								);

								this.textEditorDescription.setContent(this.descriptionClub);
								this.$watch(
									() => {
										return this.textEditorDescription.getContent();
									},
									val => {
										if (this.mountedReady && val != "") {
											this.editingDescriptionClub = true;
										}
									}
								);
							}
						});
				});
		},
		updateDescription() {
			var text = this.textEditorDescription.getContent();
			let data = {
				business_club_id: this.currentBussinesClub.clubId,
				descriptionClub: text
			};
			this.$store.dispatch("updateDescriptionClub", data).then(res => {
				this.editingDescriptionClub = false;
			});
		},
		cancelUpdateDescription() {
			this.textEditorDescription.setContent(this.descriptionClub);
			this.editingDescriptionClub = false;
		},
		addOrEditRule() {
			if (this.editingRule) {
				{
					var text = this.textEditorRules.getContent();
					if (text != "") {
						let data = {
							business_club_id: this.currentBussinesClub.clubId,
							ruleId: this.selectedRule._id,
							descriptionRule: text
						};
						this.$store.dispatch("updateRule", data).then(res => {
							for (let j = 0; j < this.editing.length; j++) {
								this.editing[j].state = false;
							}
							this.initRules();
						});
					} else {}
				}
			}
			if (this.addingRule) {
				var text = this.textEditorRules.getContent();
				if (text != "") {
					let data = {
						business_club_id: this.currentBussinesClub.clubId,
						rules: text
					};
					this.$store.dispatch("addRule", data).then(res => {
						this.initRules();
					});
				} else {}
			}
		},
		editRule(rule, i) {
			for (let j = 0; j < this.editing.length; j++) {
				this.editing[j].state = false;
			}
			this.addingRule = false;
			this.editing[i].state = true;
			this.editingRule = true;
			this.textEditorRules.setContent(rule.descriptionRule);
			this.selectedRule = rule;
		},
		cancelEditRule() {
			this.textEditorRules.setContent("");
			for (let j = 0; j < this.editing.length; j++) {
				this.editing[j].state = false;
			}
			this.editingRule = false;
			this.addingRule = false;
		},
		changeColorSlider(slider) {
			if (slider < 50) {
				this.colorSlider = "green";
			}
			if (slider >= 50 && slider <= 75) {
				this.colorSlider = "orange";
			}
			if (slider >= 75) {
				this.colorSlider = "red";
			}
		},
		deleteRule(ruleId, i) {
			let data = {
				business_club_id: this.currentBussinesClub.clubId,
				ruleId: ruleId
			};
			this.$store.dispatch("deleteRule", data).then(res => {
				this.removing.splice(i, 1);
				for (let j = 0; j < this.editing.length; j++) {
					this.editing[j].state = false;
				}
			});
		}
	}
};
</script>
<style scoped>
.custom-li {
	color: lightslategray;
}


.mobileEditor {
	height: 35vh;
}

.desktopEditor {
	height: 30vh;
}
</style>
