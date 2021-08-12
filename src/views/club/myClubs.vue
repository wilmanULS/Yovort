<template>
<v-container grid-list-xs class="no-padding">
	<v-layout row wrap>
		<v-flex xs12 sm6 md3 v-for="club in currentClubs" v-bind:key="club.club">
			<v-club-card :club="club"></v-club-card>
		</v-flex>
	</v-layout>
</v-container>
</template>
<script>
import {
	mapGetters
} from "vuex";
import Vue from "vue";
import AppConfig from "../../constants/AppConfig.js";
import vClubCard from "./clubCard";

export default {
	components: {
		'v-club-card': vClubCard,
	},
	created() {
		this.$store.dispatch("doGetMyvClubs").then(() => {});
	},
	computed: {
		...mapGetters([
			"currentClubs",
		]),
	},
	data() {
		return {};
	},
};
</script>
