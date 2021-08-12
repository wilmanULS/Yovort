<template>
  <v-layout wrap row>
    <v-flex xs12 sm12 md12 lg12 class="pa-2 text-xs-left">
      <h4 style="color:gray">{{$t('message.schedules')}}</h4>
    </v-flex>
    <v-flex xs12 sm4 md4 lg4 class="pa-2">
      <span  style="color:gray" class="text-xs-center">{{$t('message.monday')}}</span>
      <v-menu
        ref="menuLD"
        :close-on-content-click="false"
        v-model="menuLD"
        :nudge-right="40"
        :return-value.sync="timeLD"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           
          :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeLD"
          :label="$t('message.from')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker 
          :color="currentBussinesClub.theme.primary"
          v-if="menuLD"
          v-model="timeLD"
          full-width
          @change="$refs.menuLD.save(timeLD)"
        ></v-time-picker>
      </v-menu>
      <v-menu
        ref="menuLH"
        :close-on-content-click="false"
        v-model="menuLH"
        :nudge-right="40"
        :return-value.sync="timeLH"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeLH"
          :label="$t('message.to')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuLH"
          v-model="timeLH"
          full-width
          @change="$refs.menuLH.save(timeLH)"
        ></v-time-picker>
      </v-menu>
    </v-flex>

    <v-flex xs12 sm4 md4 lg4 class="pa-2">
      <span  style="color:gray" class="text-xs-center">{{$t('message.tuesday')}}</span>
      <v-menu
        ref="menuMD"
        :close-on-content-click="false"
        v-model="menuMD"
        :nudge-right="40"
        :return-value.sync="timeMD"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeMD"
          :label="$t('message.from')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuMD"
          v-model="timeMD"
          full-width
          @change="$refs.menuMD.save(timeMD)"
        ></v-time-picker>
      </v-menu>
      <v-menu
        ref="menuMH"
        :close-on-content-click="false"
        v-model="menuMH"
        :nudge-right="40"
        :return-value.sync="timeMH"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeMH"
          :label="$t('message.to')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuMH"
          v-model="timeMH"
          full-width
          @change="$refs.menuMH.save(timeMH)"
        ></v-time-picker>
      </v-menu>
    </v-flex>

    <v-flex xs12 sm4 md4 lg4 class="pa-2">
      <span  style="color:gray" class="text-xs-center">{{$t('message.wednesday')}}</span>
      <v-menu
        ref="menuWD"
        :close-on-content-click="false"
        v-model="menuWD"
        :nudge-right="40"
        :return-value.sync="timeWD"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeWD"
          :label="$t('message.from')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuWD"
          v-model="timeWD"
          full-width
          @change="$refs.menuWD.save(timeWD)"
        ></v-time-picker>
      </v-menu>
      <v-menu
        ref="menuWH"
        :close-on-content-click="false"
        v-model="menuWH"
        :nudge-right="40"
        :return-value.sync="timeWH"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeWH"
          :label="$t('message.to')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuWH"
          v-model="timeWH"
          full-width
          @change="$refs.menuWH.save(timeWH)"
        ></v-time-picker>
      </v-menu>
    </v-flex>

    <v-flex xs12 sm4 md4 lg4 class="pa-2">
      <span  style="color:gray" class="text-xs-center">{{$t('message.thursday')}}</span>
      <v-menu
        ref="menuJD"
        :close-on-content-click="false"
        v-model="menuJD"
        :nudge-right="40"
        :return-value.sync="timeJD"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeJD"
          :label="$t('message.from')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuJD"
          v-model="timeJD"
          full-width
          @change="$refs.menuJD.save(timeJD)"
        ></v-time-picker>
      </v-menu>
      <v-menu
        ref="menuJH"
        :close-on-content-click="false"
        v-model="menuJH"
        :nudge-right="40"
        :return-value.sync="timeJH"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeJH"
          :label="$t('message.to')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuJH"
          v-model="timeJH"
          full-width
          @change="$refs.menuJH.save(timeJH)"
        ></v-time-picker>
      </v-menu>
    </v-flex>

    <v-flex xs12 sm4 md4 lg4 class="pa-2">
      <span  style="color:gray" class="text-xs-center">{{$t('message.friday')}}</span>
      <v-menu
        ref="menuVD"
        :close-on-content-click="false"
        v-model="menuVD"
        :nudge-right="40"
        :return-value.sync="timeVD"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeVD"
          :label="$t('message.from')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuVD"
          v-model="timeVD"
          full-width
          @change="$refs.menuVD.save(timeVD)"
        ></v-time-picker>
      </v-menu>
      <v-menu
        ref="menuVH"
        :close-on-content-click="false"
        v-model="menuVH"
        :nudge-right="40"
        :return-value.sync="timeVH"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeVH"
          :label="$t('message.to')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuVH"
          v-model="timeVH"
          full-width
          @change="$refs.menuVH.save(timeVH)"
        ></v-time-picker>
      </v-menu>
    </v-flex>

    <v-flex xs12 sm4 md4 lg4 class="pa-2">
      <span  style="color:gray" class="text-xs-center">{{$t('message.saturday')}}</span>
      <v-menu
        ref="menuSD"
        :close-on-content-click="false"
        v-model="menuSD"
        :nudge-right="40"
        :return-value.sync="timeSD"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeSD"
          :label="$t('message.from')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuSD"
          v-model="timeSD"
          full-width
          @change="$refs.menuSD.save(timeSD)"
        ></v-time-picker>
      </v-menu>
      <v-menu
        ref="menuSH"
        :close-on-content-click="false"
        v-model="menuSH"
        :nudge-right="40"
        :return-value.sync="timeSH"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeSH"
          :label="$t('message.to')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuSH"
          v-model="timeSH"
          full-width
          @change="$refs.menuSH.save(timeSH)"
        ></v-time-picker>
      </v-menu>
    </v-flex>

    <v-flex xs12 sm4 md4 lg4 class="pa-2">
      <span  style="color:gray" class="text-xs-center">{{$t('message.sunday')}}</span>
      <v-menu
        ref="menuSD"
        :close-on-content-click="false"
        v-model="menuSUD"
        :nudge-right="40"
        :return-value.sync="timeSUD"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeSUD"
          :label="$t('message.from')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuSUD"
          v-model="timeSUD"
          full-width
          @change="$refs.menuSD.save(timeSUD)"
        ></v-time-picker>
      </v-menu>
      <v-menu
        ref="menuSH"
        :close-on-content-click="false"
        v-model="menuSUH"
        :nudge-right="40"
        :return-value.sync="timeSUH"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        max-width="290px"
        min-width="290px"
      >
        <v-text-field           :color="currentBussinesClub.theme.primary"
          slot="activator"
          v-model="timeSUH"
          :label="$t('message.to')"
          prepend-icon="access_time"
          readonly
        ></v-text-field>
        <v-time-picker        :color="currentBussinesClub.theme.primary"
          v-if="menuSH"
          v-model="timeSH"
          full-width
          @change="$refs.menuSH.save(timeSUH)"
        ></v-time-picker>
      </v-menu>
    </v-flex>
  </v-layout>
</template>
<script>

import Vue from "vue";

import { mapGetters } from "vuex";

export default {
  computed: {
    ...mapGetters([
      "userProfile",
      "countries",
      "provinces",
      "prefixes",
      "currentBussinesClub",
      "selectedLocale",
      "coordinatesSelected"
    ]),
    userProfile: {
      get() {
        if (this.$store.getters.userProfile != "")
          return JSON.parse(this.$store.getters.userProfile);
      }
    },
    coordinatesSelected: {
      get() {
        return this.$store.getters.coordinatesSelected;
      }
    }
  },
  data() {
    return {
       //Monday
      timeLD: null,
      timeLH: null,
      menuLD: false,
      menuLH: false,
      //Tuesday
      timeMD: null,
      timeMH: null,
      menuMD: false,
      menuMH: false,
      //Wendnesday
      timeWD: null,
      timeWH: null,
      menuWD: false,
      menuWH: false,
      //Thursday
      timeJD: null,
      timeJH: null,
      menuJD: false,
      menuJH: false,
      //Friday
      timeVD: null,
      timeVH: null,
      menuVD: false,
      menuVH: false,
      //Saturday
      timeSD: null,
      timeSH: null,
      menuSD: false,
      menuSH: false,
      //Sunday
      timeSUD: null,
      timeSUH: null,
      menuSUD: false,
      menuSUH: false,
    };
  }
};
</script>