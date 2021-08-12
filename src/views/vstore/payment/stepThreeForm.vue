<template>
  <v-container fluid  class="my-0 py-0">
    <v-layout row wrap>
      <v-flex xs12>
        <v-radio-group v-model="deliverySelector">
          <v-radio
           :color="currentBussinesClub.theme.primary"
            v-for="(delivery,i) in deliveryMethods"
            :key="i"
            :label="delivery.description+'$ '+delivery.cost+' ('+delivery.estimatedTime+')'"
            :value="i"
          ></v-radio>
        </v-radio-group>
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
        deliverySelector:'',
      deliveryMethods: [
        {
          description: "Entrega estándar",
          estimatedTime: "3 a 5 días",
          cost: 2.99
        },
        {
          description: "Entrega express",
          estimatedTime: "1 a 2 días",
          cost: 2.99
        },
        {
          description: "El mismo día",
          estimatedTime: "Se entrega en la noche",
          cost: 2.99
        }
      ]
    };
  }
};
</script>
