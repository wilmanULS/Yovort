<template>
<v-container class="no-padding" lazy>
    <v-layout row wrap>
        <v-jobs :class="{'mb-1':$vuetify.breakpoint.smAndDown }"></v-jobs>
        <v-skills></v-skills>
    </v-layout>
</v-container>
</template>

<script>
import Vue from "vue";
import confirmDialog from "Components/ySocialHome/confirmDialog";
import JobsInfo from "./tabJobsInfo";
import SkillsInfo from "./tabJobsSkills";

import {
    mapGetters
} from "vuex";

export default {
    props: {
        'currentTab': Number,
    },
    components: {
        "v-confirm-dialog": confirmDialog,
        "v-jobs": JobsInfo,
        "v-skills": SkillsInfo
    },
    computed: {}
};
</script>
