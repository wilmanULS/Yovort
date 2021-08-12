<template>
  <v-card @mouseover="hoverDeal = true;" @mouseout="hoverDeal = false;" class="hover-deal" @click="openProductDetail">
    <v-flex>
      <!--   <v-btn fab dark :color="currentBussinesClub.theme.secondary" absolute  class="newbutton-class" v-if="isNew">Nuevo</v-btn> -->
      <v-img :src="hotDealInfo.image1" aspect-ratio="1" v-if="!hoverDeal" class="">
        <v-hot-deal-count
          v-if="showcounter"
          :startDate="startDate"
          :endDate="endDate"
          style="position: absolute; bottom:2%"
        ></v-hot-deal-count>
      </v-img>
      <v-img :src="hotDealInfo.image2" aspect-ratio="1" class="animated fadeIn" v-else>
        <v-hot-deal-count
          v-if="showcounter"
          :startDate="startDate"
          :endDate="endDate"
          style="position: absolute; bottom:2%"
        ></v-hot-deal-count>
      </v-img>
      <v-card-title v-show="!hoverDeal" class="size-card-tittle pt-0 mt-0 pl-2">
        <v-layout align-center justify-center fill-height row wrap>
          <v-flex xs12>
            <span class="firstText font-hot-name">sueter holliester</span>
          </v-flex>
          <v-flex xs12>
            <span class="pt-0 headline font-hot-name">{{hotDealInfo.price}}</span>
          </v-flex>
          <div class="pt-0" xs12>
            <span class="through-span">{{hotDealInfo.newPrice}}</span>
          </div>
          <v-spacer></v-spacer>
          <div xs12>
            <span class="discount">
              {{hotDealInfo.discountPrice}}
              <v-icon style="font-weight:bold;">arrow_downward</v-icon>
            </span>
          </div>
        </v-layout>
      </v-card-title>
      <v-card-title
        primary-title
        v-show="hoverDeal==true"
        class="size-card-tittle animated fadeIn pt-0 px-2 mt-0"
      >
        <v-layout align-center justify-center fill-height row wrap>
          <v-flex pt-0 pb-0 mb-0 xs12>
            <v-btn
            
              dark
              :color="currentBussinesClub.theme.primary"
              style="font-size:9pt;"
            >{{$t("message.addToCar")}}</v-btn>
          </v-flex>
          <v-flex xs12>
            <v-layout>
              <v-flex xs6 pt-1>
                <center>
                  <v-icon style="font-size:14pt;">share</v-icon>
                  <span class="font-sizeCard pl-2">{{$t("message.share")}}</span>
                </center>
              </v-flex>
              <v-flex xs6 pt-1>
                <center>
                  <v-icon @click="likes()" v-if="!actualLike" style="font-size:14pt;">ti-heart</v-icon>
                  <v-icon
                    @click="likes()"
                    v-if="actualLike"
                    color="red"
                    class="animated rubberBand"
                    style="font-size:14pt;"
                  >fas fa-heart</v-icon>
                  <span class="font-sizeCard pl-2">{{$t('message.favorite')}}</span>
                </center>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-card-title>
    </v-flex>
    <v-product-detail ref="productDetail" @onCancel="closeProductDialog()" ></v-product-detail>
  </v-card>
</template>
<script>
import hotDealCount from "./hotDealCount";
import { mapGetters } from "vuex";
import productDetail from "../../components/Store/productDetail"
export default {
  computed: {
    ...mapGetters(["currentBussinesClub"])
  },
  components: {
    "v-hot-deal-count": hotDealCount,
    'v-product-detail':productDetail
  },
  methods: {
    likes() {
      this.actualLike = !this.actualLike;
    }
  },
  props: {
    like: Boolean,
    showcounter: Boolean,
    startDate: String,
    endDate: String,
    price: Number,
    discoutPrice: Number,
    picture: String,
    secondPicture: String,
    isNew: Boolean
  },
  created() {
    this.actualLike = this.like;
  },
  data() {
    return {
      actualLike: false,
      hotDealInfo: {
        name: "Kangaroo Valley Ellie Goulding new album",
        price: "$84.24",
        newPrice: "$70.65",
        discountPrice: "20%",
        image1:
          "https://rockcontent.com/wp-content/uploads/2019/02/o-que-e-produto-no-mix-de-marketing.png",
        image2:
          "https://www.jeep-outfitter.com/media/catalog/product/cache/6/image/9df78eab33525d08d6e5fb8d27136e95/o/1/o100342_a474_front_i.jpg"
      },
      hoverDeal: false,
      timeInterval: null,
      countDownDate: 0
    };
  },
  mounted() {
    //this.startCountDown();
  },
  methods:{
    openProductDetail(){
      this.$refs.productDetail.openDialog()
    },
    closeProductDialog(){
       this.$refs.productDetail.close()
    }
  }
};
</script>
<style>

.zoomIN {
  animation-duration: 1s;
}

.zoomIN {
  animation-name: zoomIN;
}
@keyframes zoomIN {
  from {
    opacity: 0;
    -webkit-transform: scale3d(0.3, 0.3, 0.3);
    transform: scale3d(0.3, 0.3, 0.3);
  }
  50% {
    opacity: 1;
  }
}
</style>
