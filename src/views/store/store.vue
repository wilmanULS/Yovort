<template>
  <div style="background-color:white;">
  <!--   <v-container class="no-padding">
      <v-flex  style="height:400px;margin-top: -18px;">
        <carousel
          class="wraperCarousel"
          width="100%"
          :dots="false"      
          :items="1"
          :navText="['<','>']"
        >
          <v-flex style="height:400px;width: 100%;display:inline-block;">
            <div style="height:400px; width:100%">
              <v-img
                src="https://www.easypromosapp.com/blog/wp-content/uploads/xxss_ideas_de_promociones_para_restaurantes.png"
                height="400px"
                width="100%"
              ></v-img>
            </div>
          </v-flex>
          <v-flex style="height:400px;width: 100%;display:inline-block;">
            <div style="height:400px; width:100%">
              <v-img
                src="https://www.easypromosapp.com/blog/wp-content/uploads/xxss_ideas_de_promociones_para_restaurantes.png"
                height="400px"
                width="100%"
              ></v-img>
            </div>
          </v-flex>
        </carousel>
      </v-flex>
    </v-container> -->
   <!--   <v-container class="px-0" style="padding-bottom: 0px;padding-top: 18px;" align-center justify-center row>
     <v-breadcrumbs style="background-color:rgb(222,222,222,0.34)" class="size-breadcrumbs" :justify-center="true">
        <v-icon
          :color="currentBussinesClub.theme.secondary"
          :size="40"
          slot="divider"
        >zmdi-chevron-right</v-icon>
        <v-breadcrumbs-item
          v-for="(item, index) in breadcrumbs"
          :key="item.text"
          :disabled="item.disabled"
        >
          <span
            v-if="index < breadcrumbs.length - 1"
            :style="'color:'+currentBussinesClub.theme.secondary+'!important;'"
          >{{ item.text }}</span>
          <span
            v-if="index == breadcrumbs.length - 1"
            :style="'color:'+currentBussinesClub.theme.secondary+'!important;'"
          >{{ item.text }}</span>
          <v-icon
            class="pl-3"
            :color="currentBussinesClub.theme.secondary"
            :size="40"
            v-if="index == breadcrumbs.length - 1"
          >zmdi-chevron-down</v-icon>
        </v-breadcrumbs-item>
      </v-breadcrumbs> 
    </v-container>-->
     <v-container class="pb-3 pa-0 pt-4" >
         <v-layout wrap row>
         <v-flex md12 lg12 xs12 sm12 xl12>
            <v-hotdeals></v-hotdeals>
            <v-items></v-items>
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
         
     </v-container> 
  </div>
</template>
<script>
import Vue from "vue";
import carousel from "vue-owl-carousel";
import { mapGetters } from "vuex";
/*  import Filter from "./filter";  */
import HotDeals from "./hotDealSlider";
import itemList from "../store/itemsList"
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
    "v-items":itemList
  },
  data() {
    return {
      page:1,
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
  },
  mounted(){

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
