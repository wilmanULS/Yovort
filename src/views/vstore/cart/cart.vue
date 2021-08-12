<template>
  <v-container>
    <v-card>
      <v-card-title primary-title class="justify-center align-center">
        <span
          style="color:grey;font-size:1.5rem;font-weight:600"
        >{{$t('message.shoppingCart').toUpperCase()}}</span>
      </v-card-title>
      <v-container grid-list-xs ma-0 fluid v-if="mountedReady">
        <v-layout row wrap>
          <v-flex xs12 sm12 md8 lg8 xl8 style="border-top:1px #e0e0e0 solid">
            <div v-if="cartItems.length!=0">
              <v-update-form-item ref="updateFormItem"></v-update-form-item>
              <v-cart-item v-for="(item,i) in cartItems" :key="i" :item="item"></v-cart-item>
            </div>
            <v-layout v-if="cartItems.length==0" justify-center align-center>
              <span>{{$t('message.noItemsCartMessage')}}</span>
            </v-layout>
          </v-flex>
          <v-flex xs12 sm12 md4 lg4 xl4 :style="$vuetify.breakpoint.smAndDown?'margin-top:20px':''" >
            <v-cart-options :subtotal="cartSubtotal"></v-cart-options>
          </v-flex>
          <v-flex xs12 sm12 md8 lg8 xl8 >
            <v-layout row wrap>
              <v-flex xs6 sm6 md3 lg3 xl3>
                <v-btn v-if="cartItems.length!==0"
                  @click="emptyCart()"
                  flat
                  :style="'background-color:'+currentBussinesClub.theme.primary"
                  class="ma-0 pa-0 mx-1"
                  color="white">
                  <v-icon class="ma-0 pa-0" style="font-size:1.2rem">mdi-window-close</v-icon>
                  <span style="font-size:0.8rem">{{$t('message.clearCart')}}</span>
                </v-btn>
                <v-btn v-else  disabled class="ma-0 pa-0 mx-1" >
                  <v-icon class="ma-0 pa-0" style="font-size:1.2rem">mdi-window-close</v-icon>
                  <span style="font-size:0.8rem">{{$t('message.clearCart')}}</span>
                </v-btn>
              </v-flex>
              <v-flex xs6 sm6 md3 lg3 xl3>
                <v-btn
                  @click="refreshCart()"
                  flat
                  :style="'background-color:'+currentBussinesClub.theme.primary"
                  class="ma-0 pa-0 mx-1"
                  color="white"
                >
                  <v-icon class="ma-0 pa-0" style="font-size:1.2rem">mdi-refresh</v-icon>
                  <span style="font-size:0.8rem">{{$t('message.updateCart')}}</span>
                </v-btn>
              </v-flex>
              <v-flex xs12 sm12 md1 lg1 xl1></v-flex>
              <v-flex sm12 xs12 md5 lg5 xl5>
                <v-btn
                  to="/vstore"
                  flat
                  :style="'background-color:'+currentBussinesClub.theme.primary"
                  class="ma-0 pa-0 mx-1"
                  color="white"
                >
                  <v-icon class="ma-0 pa-0" style="font-size:1.2rem">mdi-chevron-left</v-icon>
                  <span style="font-size:0.8rem">{{$t('message.continueShopping')}}</span>
                </v-btn>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-container>
    </v-card>
    
  </v-container>
</template>
<script>
import { mapGetters } from "vuex";
import itemCart from "./itemCart";
import lateralOptions from "./lateralOptionsCart";
import updateItemForm from "./updateItemDialog"
export default {
  computed: {
    ...mapGetters(["cartItems", "cartSubtotal", "currentBussinesClub"])
  },
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
          msg: this.$t("message.unknownError"),
          type: "error"
        });
        this.$root.$children[0].$emit("hideLoading");
      });
  },
  data() {
    return {
      mountedReady: false
    };
  },
  methods: {
    emptyCart() {
      this.$root.$children[0].$emit(
        "showLoading",
        this.$t("message.emptyingCart")
      );
      this.$store
        .dispatch("emptyItemsToCart")
        .then(() => {
          this.$root.$children[0].$emit("hideLoading");
          this.$store.dispatch("doNotification", {
          msg: this.$t("message.cartEmptySuccess"),
          type: "success"
        });
        })
        .catch(() => {
          this.$root.$children[0].$emit("hideLoading");
        });
    },
    refreshCart() {
      this.$root.$children[0].$emit(
        "showLoading",
        this.$t("message.loadingfInformation")
      );
      this.$store.dispatch("getCartItems").then(res => {
        this.$root.$children[0].$emit("hideLoading");
      });
    },

  },
  components: {
    "v-cart-item": itemCart,
    "v-cart-options": lateralOptions,
    "v-update-form-item":updateItemForm
  }
};
</script>
<style>
.expandText:hover {
  transition: 100ms;
  color: aqua;
}
.expandText {
  transition: 100ms;
  color: red;
}
.customExpansionItem .v-expansion-panel__header {
  padding: 0px 0px;
  transition: 100ms;
}
.customExpansionItem .v-expansion-panel__header:hover {
  padding: 10px 0px;
  transition: 200ms;
  color: purple;
}
.customExpansionPanel .v-expansion-panel {
  -webkit-box-shadow: none;
}
</style>

