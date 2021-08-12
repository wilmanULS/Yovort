<template>
  <v-container fluid  class="my-0 py-0">
    <v-layout row wrap>
      <v-flex xs12>
        <v-radio-group v-model="paymentSelector">
          <v-radio
            :color="currentBussinesClub.theme.primary"
            v-for="(paymentMethod,i) in paymentMethods"
            :key="i"
            :label="paymentMethod.description"
            :value="i"
          ></v-radio>
        </v-radio-group>
      </v-flex>
       <v-flex xs12 sm12 md6 lg6 xl6>
           <v-select
          :color="currentBussinesClub.theme.secondary"
          :items="months"
          v-model="month"
          :label="$t('message.month')"
        ></v-select>
       </v-flex>
              <v-flex xs12 sm12 md6 lg6 xl6>
           <v-select
          :color="currentBussinesClub.theme.secondary"
          :items="years"
          v-model="year"
          :label="$t('message.year')"
        ></v-select>
       </v-flex>
       <v-flex xs12 sm12 md6 lg6 xl6>
        <v-text-field
          :color="currentBussinesClub.theme.secondary"
          hide-details
          label="CVV"
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
      paymentSelector: "",
      paymentMethods: [
        {
          description: "Tarjeta de crédito",
        },
        {
          description: "PayPal",
        }
      ],
      month:'Enero',
      months:['Enero'],
      year:2019,
      years:[2019,2020,2021]
    };
  }
};
</script>
