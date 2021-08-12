<template>
  <div style="background-color:white;">
    <v-container class="pb-3 pa-0 pt-4">
      <v-layout wrap row>
        <v-flex md12 lg12 xs12 sm12 xl12>
          <!-- <v-hotdeals></v-hotdeals> -->
          <v-products></v-products>
          <div>
            <v-pagination
              class="pt-2"
              :color="currentBussinesClub.theme.primary"
              v-model="page"
              :length="6"
            ></v-pagination>
          </div>
        </v-flex>
      </v-layout>
      <v-btn to="/vstore/cart" style="position:fixed;right:0px;bottom:4px" :color="currentBussinesClub.theme.primary" fab dark>
        <v-icon small>mdi-cart-arrow-right</v-icon>
      </v-btn>
    </v-container>
  </div>
</template>
<script>
import Vue from "vue";
import carousel from "vue-owl-carousel";
import { mapGetters } from "vuex";
/*  import Filter from "./filter";  */
import HotDeals from "./hotDealSlider";
import productsSection from "./productsSection";
import router from "../../router";
export default {
  computed: {
    ...mapGetters(["currentBussinesClub"]),
    currentUserBusiness: {
      get() {
        if (this.$store.getters.currentUserBusiness != "")
          return this.$store.getters.currentUserBusiness;
      }
    }
  },
  components: {
    carousel,
    "v-hotdeals": HotDeals,
    /* "v-filter-shop":Filter, */
    "v-products": productsSection
  },
  data() {
    return {
      page: 1,
      breadcrumbs: [
        {
          text: "Categoría seleccionada",
          disabled: false
        },
        {
          text: "Subcategoría seleccionada",
          disabled: false
        },
        {
          text: "Subcategoría a seleccionar",
          disabled: false
        }
      ]
    };
  }
};
</script>
<style less>
:root {
  --colorSecondary: #ffffff;
  --colorPrimary: #ffffff;
  --bottomVar: 0;
}
.v-card--reveal {
  align-items: center;
  bottom: 0;
  justify-content: center;

  position: absolute;
  width: 100%;
}
.wraperCarousel .owl-prev {
  background-color: transparent !important;
  border-radius: 50% !important;
  width: 30px !important;
  height: 30px !important;
  border: 2px solid var(--colorButtom) !important;
  padding-top: 1px !important;
  padding-right: 8px !important;
  padding-left: 3px !important;
  position: absolute;
  top: 44% !important;
  right: calc(100% - 28px);
  transform: translateY(-50%) !important;
  color: black !important;
  line-height: 5px;
  font-size: 80px !important;
  transform: scale(0.7, 1.5) !important;
}
.wraperCarousel .owl-next {
  background-color: transparent !important;
  border-radius: 50% !important;
  width: 30px !important;
  height: 30px !important;
  border: 2px solid transparent !important;
  padding-top: 1px !important;
  padding-left: 3px !important;
  position: absolute;
  left: calc(100% - 52px);
  top: 44% !important;
  transform: translateY(-50%) !important;
  color: black !important;
  line-height: 5px;
  font-size: 80px !important;
  transform: scale(0.7, 1.5) !important;
  /*  display: none !important; */
}

/* .wraperCarousel:hover .owl-next{
  display: block!important;
}
.wraperCarousel .owl-nav{
  position: absolute;
  top: 44% !important; 
} */
</style>
