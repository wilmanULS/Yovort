<template>
<v-container grid-list-xs px-5>
	<center>
		<v-img width="500px" src="/static/img/under_construction.gif"></v-img>
	</center>
</v-container>
</template>
<script>
import {
	createEditor
} from "vueditor";
import {
	mapGetters
} from "vuex";
export default {
	data() {
		return {
			textEditor1: null,
			editDescription: false,
			descriptionInput: "description of bussines club rules for test on view test :C",
			inputRule: "",
			hindMessage: "<span style='color:#9c28b2'>Añadiendo regla, por favor espere..</span>",
			addLoading: false,
			removing: false,
			bussinesRules: ["Rule One", "Rule Two", "Rule Three", "Rule Four"],
			headerRulesDesktop: [{
					text: "Reglas",
					align: "left",
					sortable: false,
					width: "70%",
					align: "left"
				},
				{
					text: "",
					sortable: false,
					width: "30%",
					align: "right"
				}
			],
			headerRulesMobile: [{
					text: "Reglas",
					align: "left",
					sortable: false,
					width: "50%",
					align: "left"
				},
				{
					text: "",
					sortable: false,
					width: "50%",
					align: "right"
				}
			],
			totalGoal: 40,
			goalsObject: [{
					goalName: "Alcanzar 10 invitados",
					goalType: "Meta Individual",
					goalState: "4",
					goalBenefit: "Gana 10vP",
					goalPercent: 15
				},
				{
					goalName: "Completar Perfil",
					goalType: "Meta Individual",
					goalState: "8",
					goalBenefit: "Gana 10vP",
					goalPercent: 59
				},
				{
					goalName: "Crear un Business Club",
					goalType: "Meta Individual",
					goalState: "4",
					goalBenefit: "Gana 10vP",
					goalPercent: 80
				},
				{
					goalName: "Alcanzar 10 invitados",
					goalType: "Meta Individual",
					goalState: "4",
					goalBenefit: "Gana 10vP",
					goalPercent: 15
				},
				{
					goalName: "Completar Perfil",
					goalType: "Meta Individual",
					goalState: "8",
					goalBenefit: "Gana 10vP",
					goalPercent: 59
				},
				{
					goalName: "Crear un Business Club",
					goalType: "Meta Individual",
					goalState: "4",
					goalBenefit: "Gana 10vP",
					goalPercent: 80
				},
				{
					goalName: "Alcanzar 10 invitados",
					goalType: "Meta Individual",
					goalState: "4",
					goalBenefit: "Gana 10vP",
					goalPercent: 15
				},
				{
					goalName: "Completar Perfil",
					goalType: "Meta Individual",
					goalState: "8",
					goalBenefit: "Gana 10vP",
					goalPercent: 59
				},
				{
					goalName: "Crear un Business Club",
					goalType: "Meta Individual",
					goalState: "4",
					goalBenefit: "Gana 10vP",
					goalPercent: 80
				}
			],
			colorSlider: "",
			slider: 30,
			widthBar2: 22,
			widthBar: 74,
			gradiantColor: "",
			dialogCardGoal: false,
			search: "",
			headers: [{
					text: "Dessert (100g serving)",
					align: "left",
					sortable: false,
					value: "name"
				},
				{
					text: "Calories",
					value: "calories"
				},
				{
					text: "Fat (g)",
					value: "fat"
				},
				{
					text: "Carbs (g)",
					value: "carbs"
				},
				{
					text: "Protein (g)",
					value: "protein"
				},
				{
					text: "Iron (%)",
					value: "iron"
				}
			],
			desserts: [{
					value: false,
					name: "Frozen Yogurt",
					calories: 159,
					fat: 6.0,
					carbs: 24,
					protein: 4.0,
					iron: "1%"
				},
				{
					value: false,
					name: "Ice cream sandwich",
					calories: 237,
					fat: 9.0,
					carbs: 37,
					protein: 4.3,
					iron: "1%"
				},
				{
					value: false,
					name: "Eclair",
					calories: 262,
					fat: 16.0,
					carbs: 23,
					protein: 6.0,
					iron: "7%"
				},
				{
					value: false,
					name: "Cupcake",
					calories: 305,
					fat: 3.7,
					carbs: 67,
					protein: 4.3,
					iron: "8%"
				},
				{
					value: false,
					name: "Gingerbread",
					calories: 356,
					fat: 16.0,
					carbs: 49,
					protein: 3.9,
					iron: "16%"
				},
				{
					value: false,
					name: "Jelly bean",
					calories: 375,
					fat: 0.0,
					carbs: 94,
					protein: 0.0,
					iron: "0%"
				},
				{
					value: false,
					name: "Lollipop",
					calories: 392,
					fat: 0.2,
					carbs: 98,
					protein: 0,
					iron: "2%"
				},
				{
					value: false,
					name: "Honeycomb",
					calories: 408,
					fat: 3.2,
					carbs: 87,
					protein: 6.5,
					iron: "45%"
				},
				{
					value: false,
					name: "Donut",
					calories: 452,
					fat: 25.0,
					carbs: 51,
					protein: 4.9,
					iron: "22%"
				},
				{
					value: false,
					name: "KitKat",
					calories: 518,
					fat: 26.0,
					carbs: 65,
					protein: 7,
					iron: "6%"
				}
			],
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
					"insertUnorderedList",
					"|",
					"picture",
					"tables",
					"|",
					"switchView"
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
				classList: []
			}
		};
	},
	computed: {
		...mapGetters(["userProfile", "currentBussinesClub"])
	},
	mounted() {},
	components: {},
	methods: {
		/*     METHODS FOR RULES  */
		addRule() {
			this.addLoading = true;
			setTimeout(() => {
				this.addLoading = false;
				this.inputRule = "";
			}, 2000);
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
		}
	}
};
</script>
<style scoped>
.padding-top-xs-10 {
	padding-top: 10px;
}

.padding-xs-10 {
	padding: 0;
}

.padding-left-title {
	padding-left: 0px !important;
}

.scroll-area {
	position: relative;
	margin: auto;
	height: 400px;
}

.bg-color-grey {
	background-color: #f4f7fa !important;
}

.bar {
	width: 100%;
	overflow: hidden;
	padding-left: 0px;
	padding-top: 5px;
	padding-right: 5px;
	padding-bottom: 0px;
	max-height: 30px;
}

.bar2 {
	width: 100%;
	overflow: hidden;
	padding-left: 0px;
	padding-top: 0px;
	padding-right: 5px;
	padding-bottom: 5px;
	max-height: 5px;
}

.progress {
	border-top-right-radius: 25px;
	border-bottom-right-radius: 25px;
	float: left;
	height: 10px;
	padding: 15px;
}

.progressFinish {
	border-bottom-right-radius: 25px;
	float: left;
	height: 10px;
	padding: 15px;
	max-height: 5px;
}

.percent {
	float: right;
	font-weight: 500;
	height: 30px;
	line-height: 30px;
}

.firstText {
	overflow: hidden;
	white-space: nowrap;
	text-overflow: ellipsis;
	width: 100%;

	display: inline-block;
}

.border-bottom-1px-grey {
	border-bottom: 1px solid #cfd8dc;
}

.border-1px-grey {
	border: solid 1px #cfd8dc;
}

.border-top-1px-grey {
	border-top: 1px solid #cfd8dc;
}

@media only screen and (max-width: 650px) {
	.padding-top-xs-10 {
		padding-top: 0px;
	}

	.padding-xs-10 {
		padding-top: 10px;
	}

	.scroll-area {
		position: relative;
		margin: auto;
		height: 350px;
	}
}

@media screen and (max-width: 1265px) {
	.padding-left-title {
		padding-left: 10px !important;
	}
}
</style>
