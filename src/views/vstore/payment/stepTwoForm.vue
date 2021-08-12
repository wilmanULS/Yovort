<template>
  <v-container fluid class="my-0 py-0">
    <v-layout row wrap>
      <v-flex xs12>
          <v-checkbox
            :color="currentBussinesClub.theme.primary"
            :label="$t('message.sameAddressShippingMessage')"
          ></v-checkbox>
        </v-flex>
      <v-flex xs12 sm12 md6 lg6 xl6>
        <v-text-field
          :color="currentBussinesClub.theme.secondary"
          hide-details
          :label="$t('message.name')"
        ></v-text-field>
      </v-flex>
      <v-flex xs12 sm12 md6 lg6 xl6>
        <v-text-field
          :color="currentBussinesClub.theme.secondary"
          hide-details
          :label="$t('message.lastName')"
        ></v-text-field>
      </v-flex>
      <v-flex xs6>
        <v-text-field
          :color="currentBussinesClub.theme.secondary"
          hide-details
          :label="$t('message.identification')"
        ></v-text-field>
      </v-flex>
      <v-flex xs6>
        <v-text-field
          :color="currentBussinesClub.theme.secondary"
          hide-details
          :label="$t('message.telephoneNumber')"
        ></v-text-field>
      </v-flex>
      <v-flex xs12>
        <v-text-field
          :color="currentBussinesClub.theme.secondary"
          hide-details
          :label="$t('message.email')"
        ></v-text-field>
      </v-flex>
      <v-flex xs12>
        <v-select
          :color="currentBussinesClub.theme.secondary"
          :items="countries"
          v-model="country"
          :label="$t('message.country')"
        ></v-select>
      </v-flex>
      <v-flex xs12 sm12 md6 lg6 xl6>
        <v-select
          :color="currentBussinesClub.theme.secondary"
          :items="states"
          v-model="state"
          :label="$t('message.stateProvince')"
        ></v-select>
      </v-flex>
      <v-flex xs12 sm12 md6 lg6 xl6>
        <v-text-field
          small
          :color="currentBussinesClub.theme.secondary"
          hide-details
          :label="$t('message.vBusinessCodePostal')"
        ></v-text-field>
      </v-flex>
      <v-flex xs12>
        <v-text-field
          :color="currentBussinesClub.theme.secondary"
          hide-details
          :label="$t('message.address')+' 1'"
        ></v-text-field>
      </v-flex>
      <v-flex xs12>
        <v-text-field
          :color="currentBussinesClub.theme.secondary"
          hide-details
          :label="$t('message.address')+' 2'"
        ></v-text-field>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  computed: {
    ...mapGetters(["currentBussinesClub"])
  },
  data() {
    return {
      countries: ["ECUADOR"],
      states: ["PICHINCHA"],
      country: "",
      state: ""
    };
  }
};
</script>
