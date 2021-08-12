<template>
<v-container class="no-padding" lazy>
    <v-layout row wrap class="mb-1">
        <v-hobby :hobbyData="hobbiesArray" :type="'Hobbies'" :field="'hobby'" className="mr05" :class="{'mb-1':$vuetify.breakpoint.smAndDown }"></v-hobby>
        <v-hobby :hobbyData="musicArray" :type="'Music'" :field="'artist_music'" className="ml05"></v-hobby>
    </v-layout>
    <v-layout row wrap class="mb-1">
        <v-hobby :hobbyData="tvShowsArray" :type="'TVShows'" :field="'tv'" className="mr05" :class="{'mb-1':$vuetify.breakpoint.smAndDown }"></v-hobby>
        <v-hobby :hobbyData="booksArray" :type="'Books'" :field="'book'" className="ml05"></v-hobby>
    </v-layout>
    <v-layout row wrap class="mb-1">
        <v-hobby :hobbyData="writersArray" :type="'Writers'" :field="'writer'" className="mr05" :class="{'mb-1':$vuetify.breakpoint.smAndDown }"></v-hobby>
        <v-hobby :hobbyData="moviesArray" :type="'Movies'" :field="'movie'" className="ml05"></v-hobby>
    </v-layout>
    <v-layout row wrap>
        <v-hobby :hobbyData="gamesArray" :type="'Games'" :field="'game'" className="mr05" :class="{'mb-1':$vuetify.breakpoint.smAndDown }"></v-hobby>
        <v-hobby :hobbyData="othersArray" :type="'Others'" :field="'other'" className="ml05"></v-hobby>
    </v-layout>
</v-container>
</template>
<script>
import Vue from "vue";
import Hobby from './tabHobbiesItem.vue'
import {
    mapGetters
} from "vuex";
export default {
    props: {
        'currentTab': Number,
    },
    data() {
        return {
            activeDark: false,
            disabledButon: true,
            recoveryDataHobbies: [],
            hobbiesArray: [],
            musicArray: [],
            tvShowsArray: [],
            booksArray: [],
            moviesArray: [],
            writersArray: [],
            gamesArray: [],
            othersArray: [],
        };
    },
    computed: {
        ...mapGetters(["userProfile", "currentBussinesClub"]),
        userProfile: {
            get() {
                if (this.$store.getters.userProfile != '')
                    return JSON.parse(this.$store.getters.userProfile);
            }
        }
    },
    watch: {
        userProfile() {
            Vue.nextTick(() => {
                this.chargeDataHobbies();
            });
        },
    },
    components: {
        "v-hobby": Hobby,
    },
    created() {
        this.chargeDataHobbies();
    },
    methods: {
        chargeDataHobbies() {
            (this.recoveryDataHobbies = this.userProfile.hobby),
            (this.hobbiesArray = this.recoveryDataHobbies.hobby),
            (this.musicArray = this.recoveryDataHobbies.artist_music),
            (this.tvShowsArray = this.recoveryDataHobbies.tv),
            (this.booksArray = this.recoveryDataHobbies.book),
            (this.moviesArray = this.recoveryDataHobbies.movie),
            (this.writersArray = this.recoveryDataHobbies.writer),
            (this.gamesArray = this.recoveryDataHobbies.game),
            (this.othersArray = this.recoveryDataHobbies.other);
        },
        mouseOver: function() {
            this.active = !false;
        },
    }
};
</script>
