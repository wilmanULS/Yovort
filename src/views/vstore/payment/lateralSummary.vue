<template>
  <v-container grid-list-xs mx-1 px-1 >
    <v-container grid-list-xs>
      <v-layout row wrap>
        <v-flex xs6 text-xs-center>
          <span style="font-size:0.8rem;color:grey">{{$t('message.product').toUpperCase()}}</span>
        </v-flex>
        <v-flex xs3 text-xs-center>
          <span style="font-size:0.8rem;color:grey">{{$t('message.quantity').toUpperCase()}}</span>
        </v-flex>
        <v-flex xs3 text-xs-center>
          <span style="font-size:0.8rem;color:grey">{{$t('message.price').toUpperCase()}}</span>
        </v-flex>
      </v-layout>
    </v-container>
    <div v-for="(item,i) in cartItems" :key="i">
      <v-item-summary :item="item"></v-item-summary>
      <v-divider></v-divider>
    </div>
    <v-card flat color="transparent">
      <v-card-actions>
        <v-spacer></v-spacer>
        <span
          style="font-size:1rem;font-weight:600;color:grey"
        >{{$t('message.subtotal').toUpperCase()}}:&nbsp;${{cartSubtotal}}</span>
      </v-card-actions>
    </v-card>
    <v-card flat>
      <v-card-title primary-title>
        <span style="font-size:1rem;font-weight:600;color:grey">{{$t('message.applyPromoCode').toUpperCase()}}</span>
      </v-card-title>
      <v-card-text>
        <v-text-field
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
  </v-container>
</template>
<script>
import { mapGetters } from "vuex";
import itemSummary from "./itemSummary";
export default {
    mounted() {
    this.$root.$children[0].$emit(
      "showLoading",
      this.$t("message.loadingfInformation")
    );
    this.$store
      .dispatch("getCartItems")
      .then(res => {
        this.mountedReady = true;
        this.$root.$children[0].$emit("hideLoading");
      })
      .catch(err => {
        this.mountedReady = true;
        this.$store.dispatch("doNotification", {
          msg: this.$t("message.noItemsCartMessage"),
          type: "error"
        });
        this.$root.$children[0].$emit("hideLoading");
      });
  },
  computed: {
    ...mapGetters(["currentBussinesClub","cartItems", "cartSubtotal"])
  },
  data() {
    return {
      mountedReady:false
    };
  },
  components: {
    "v-item-summary": itemSummary
  }
};
</script>
<style>
</style>
