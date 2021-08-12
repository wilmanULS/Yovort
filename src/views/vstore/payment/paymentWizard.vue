<template>
  <v-container fluid>
    <v-card flat>
      <v-card-title class="justify-center align-center">
        <span
          style="font-size:1.5rem;font-weight:600;color:grey"
        >{{$t('message.checkoutWizard').toUpperCase()}}</span>
      </v-card-title>
      <v-container fluid>
        <v-layout row wrap>
          <v-flex xs12 sm12 md8 lg8 xl8>
              <v-steper-payment></v-steper-payment>
          </v-flex>
          <v-flex xs12 sm12 md4 lg4 xl4 >
            <div>
              <v-summary></v-summary>
            </div>
          </v-flex>
        </v-layout>
      </v-container>
    </v-card>
  </v-container>
</template>
<script>
import { mapGetters } from "vuex";
import stepperPayment from "./paymentStepper"
import lateralSummary from "./lateralSummary"
export default {
  computed: {
    ...mapGetters(["currentBussinesClub"])
  },
  data() {
    return {
      color: "purple",
      controlStepper: 1,
      stepProgress:25,
    };
  },
  components: {
    "v-steper-payment": stepperPayment,
    "v-summary":lateralSummary
  },
};
</script>

