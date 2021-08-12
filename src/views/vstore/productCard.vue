<template>
  <v-card flat>
    <v-layout flat row wrap>
      <v-flex
        hover
        pt-2
        style="text-align:left"
        xs12
        md4
        sm4
        lg4>
        <v-card flat >
          <v-layout row wrap align-center justify-center>
            <img style="height:190px" :src="product.media[0].url" v-show="!hoverDeal">
          </v-layout>
        </v-card>
      </v-flex>
      <v-flex xs12 md8 sm8 lg8 class="pt-0 mt-0">
        <v-card-title primary-title class="pt-0 mt-0 pb-0 mb-0">
          <div>
            <span class="darkTitles headline titleProductsListView">{{getObjectPath(product.names[0], 'name','')}}</span>
            <br>
            <span style="font-size:21px!important; " class="darkTitles pb-0">${{getObjectPath(product.price.single,'vMoney','')}}</span>
            <v-flex xs12>
              <span class="priceProductSubrayed">$14.19</span>
            </v-flex>
            <span
              class="textDescription"
            >{{getObjectPath(product.names[0],'description.short','')}}</span>
          </div>
        </v-card-title>
        <v-card-actions class="pd-top pl-3 mt-0">
          <v-flex v-if="$vuetify.breakpoint.lgAndUp" lg4 xs3>
            <v-btn @click="addToCart()" dark :color="currentBussinesClub.theme.primary">{{$t('message.addToCar')}}</v-btn>
          </v-flex>
          <v-flex class="pl-3" v-if="$vuetify.breakpoint.lgAndUp" xs4>
            <v-icon style="font-size:25pt;">share</v-icon>
            <span class="pl-3 colorTextIcons">{{$t('message.share')}}</span>
          </v-flex>
          <v-flex v-if="$vuetify.breakpoint.lgAndUp" xs4>
            <v-icon @click="likes()" v-if="actualLike" style="font-size:25pt;">ti-heart</v-icon>
            <v-icon
              @click="likes()"
              v-if="!actualLike"
              :color="currentBussinesClub.theme.secondary"
              class="animated rubberBand"
              style="font-size:25pt;"
            >fas fa-heart</v-icon>
            <span class="pl-3 colorTextIcons">{{$t("message.favorite")}}</span>
          </v-flex>
          <v-layout row wrap>
            <v-flex v-if="$vuetify.breakpoint.mdAndDown" xs12 class="text-xs-center">
              <div class="pb-3">
                <v-btn @click="addToCart()" dark :color="currentBussinesClub.theme.primary">
                  {{$t("message.add")}}
                  <v-icon style="padding-left:5px!important ;font-size:12pt;">add_shopping_cart</v-icon>
                </v-btn>
              </div>
            </v-flex>
            <v-flex v-if="$vuetify.breakpoint.mdAndDown" xs6 class="text-xs-center">
              <div>
                <v-icon small class="pr-4">share</v-icon>
                <span class="colorTextIcons">{{$t('message.share')}}</span>
              </div>
            </v-flex>
            <v-flex v-if="$vuetify.breakpoint.mdAndDown" xs6 class="text-xs-center">
              <div>
                <v-icon small @click="likes()" v-if="actualLike">ti-heart</v-icon>
                <v-icon
                  small
                  @click="likes()"
                  v-if="!actualLike"
                  
                  :color="currentBussinesClub.theme.secondary"
                  class="animated rubberBand"
                >fas fa-heart</v-icon>
                <span class="pl-3 colorTextIcons">{{$t("message.favorite")}}</span>
              </div>
            </v-flex>
          </v-layout>
        </v-card-actions>
      </v-flex>
    </v-layout>
    <v-divider></v-divider>
  </v-card>
</template>
 <script>
 import Vue from "vue";
import { mapGetters } from "vuex";
export default {
  computed: {
    ...mapGetters(["currentBussinesClub"])
  },
  created() {
    this.actualLike = this.like;
  },
  data() {
    return {
      getObjectPath: Vue.getObjectPath,
      actualLike: true,
      hoverDeal: false
    };
  },
  props: {
    product: {
      type: Object
    }
  },
  methods: {
    likes() {
      this.actualLike = !this.actualLike;
    },
    addToCart(){
      this.$store.dispatch("setCurrentProductStore",this.product)
      .then(res=>{
          this.$parent.$refs.productDetail.openDialog()
      })
    }
  }
};
</script>
 <style>
.priceProductSubrayed {
  font-size: 13px !important;
  text-decoration-line: line-through;
  color: #757575;
}
.titleProductsListView {
  font-size: 14pt !important;
  padding-bottom: 10px !important;
}
.colorTextIcons {
  color: #363636 !important;
}
.textDescription {
  font-size: 14px !important;
  color: #757575;
}
.darkTitles {
  color: black !important;
}
.heart3 {
  max-height: 70px;
  fill: white;
  stroke: white;
  cursor: pointer;
}
.pd-top {
  padding-top: 20px !important;
}
@media (max-width: 600px) {
  .pd-top {
    padding-top: 15px !important;
  }
}
</style>
 