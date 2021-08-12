<template>
  <v-card flat :class="!$vuetify.breakpoint.smAndDown?'pl-2':''" >
    <v-card-title primary-title class="align-center ma-0 pa-0">
      <span
        style="font-size:1.4rem;font-weight:600;color:grey"
      >{{$t('message.subtotal').toUpperCase()}}:&nbsp;${{subtotal}}</span>
    </v-card-title>
    <v-btn v-if="cartItems.length!==0" to="/vstore/checkout" flat large block :style="'background-color:'+currentBussinesClub.theme.primary">
      <span style="color:white">{{$t('message.proceedToCheckout')}}</span>
    </v-btn>
    <v-btn disabled v-else large block :color="currentBussinesClub.theme.primary" >
      <span style="color:white">{{$t('message.proceedToCheckout')}}</span>
    </v-btn>
    <br>
    <v-divider></v-divider>
    <div class="customExpansionPanel">
      <v-expansion-panel v-model="expand" expand>
        <v-expansion-panel-content flat ref="xpand" class="customExpansionItem">
          <v-flex slot="header">
            <span
              style="font-size:1rem;font-weight:600"
            >{{$t('message.applyPromoCode').toUpperCase()}}</span>
          </v-flex>
          <v-card>
            <v-card-text class="pa-0">
              <v-text-field
                class="ma-0 pa-0"
                :color="currentBussinesClub.theme.secondary"
                hide-details
                :label="$t('message.code')">
                <template slot="append-outer">
                  <v-btn class="ma-0 pa-0 ml-0" small :color="currentBussinesClub.theme.primary">
                    <span style="color:white">{{$t('message.apply')}}</span>
                  </v-btn>
                </template>
              </v-text-field>
            </v-card-text>
          </v-card>
        </v-expansion-panel-content>
        <v-expansion-panel-content ref="xpand" class="customExpansionItem">
          <v-flex slot="header">
            <span
              color="currentBussinesClub.theme.secondary"
              style="font-size:1rem;font-weight:600"
            >{{$t('message.shippingEstimates').toUpperCase()}}</span>
          </v-flex>
          <v-card>
            <v-select
              :color="currentBussinesClub.theme.secondary"
              :items="countries"
              v-model="country"
              :label="$t('message.country')"
            ></v-select>
            <v-select
              :color="currentBussinesClub.theme.secondary"
              :items="states"
              v-model="state"
              :label="$t('message.stateProvince')"
            ></v-select>
            <v-text-field
              :color="currentBussinesClub.theme.secondary"
              hide-details
              :label="$t('message.vBusinessCodePostal')"
            ></v-text-field>
          </v-card>
        </v-expansion-panel-content>
        <v-expansion-panel-content ref="xpand" class="customExpansionItem">
          <v-flex slot="header">
            <span
              style="font-size:1rem;font-weight:600"
            >{{$t('message.orderComment').toUpperCase()}}</span>
          </v-flex>
          <v-card>
            <v-textarea
              auto-grow
              rows="3"
              :color="currentBussinesClub.theme.secondary"
              :label="$t('message.writeCommentHere')"
              line
              value
            ></v-textarea>
          </v-card>
        </v-expansion-panel-content>
      </v-expansion-panel>
    </div>
  </v-card>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  computed: {
    ...mapGetters(["currentBussinesClub","cartItems"])
  },
  data() {
    return {
      expand: [false, false, false],
      countries: ["ECUADOR"],
      states: ["PICHINCHA"],
      country: "",
      state: "",
      postalCode: ""
    };
  },
  props: {
    subtotal: ""
  }
};
</script>
